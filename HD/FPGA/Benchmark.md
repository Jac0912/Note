# 1. background
QDMA: 同 XDMA 一样是赛灵思公司对于 QDMA 技术的具体实现

它具有以下优势：
- 引入生产者、消费者
	- 软件（生产者）：CPU 维护描述符队列（任务列表），CPU 可以不断往里面扔任务
	- 硬件（消费者）：QDMA 引擎去内存中抓取任务执行
- 支持多个队列数量：每个虚拟机或者应用程序都可以有自己的传输通道
- 描述符预取：把任务清单放到硬件内部缓存
- 支持虚拟化（SR-IOV）

说明：
1. 硬件侧代码：https://github.com/RC4ML/rc4ml_static
2. 软件侧代码：https://github.com/RC4ML/rc4ml_qdma
3. 测试 GPU to FPGA 时需要让 GPU 跑一些核函数，拉高 GPU power 防止出现 throughput 过低
4. 下面分别对 throughput / random / latency / mmio 几个方面进行测试，包含 gpu / cpu 与 fpga 之前 h2c / c2h 两个方向的测试

| target           | mean            | func                                                  | note                 |
| ---------------- | --------------- | ----------------------------------------------------- | -------------------- |
| Throughput(GB/s) | 每秒传送数据量         | $$\frac{length\times total\_cmds}{cycles\times gap}$$ | length(GB)<br>gap(s) |
| Throughput(Mops) | 每秒执行的操作数（百万次/秒） | $$\frac{total\_cmds}{cycles\times gap \times 10^6}$$  | gap(s)               |
| Latency (us)     | 单次命令的平均延迟       | $$\frac{cycles\times gap}{total\_cmds}$$              | gap(us)              |
| OPS limit        | OPS 的理论限制       | $$\frac{f}{wait \; cycle}$$                           |                      |

Testbed: amax2, U280 board.

---
This benchmark the axi-lite read latency when dma channel is busy.
## 1.1. AXILite latency

| AXIL Latency           |              | QDMA Bandwidth  |                |
| ---------------------- | ------------ | --------------- | -------------- |
| axi lite read(yes/no)  | latency(us)  | read(GBps)      | write(GBps)    |
| no                     | /            | 12.79           | 12.99          |
| yes                    | 0.88         | 0               | 0              |
| yes                    | 0.95         | 12.79           | 0              |
| yes                    | 1.47         | 0               | 12.98          |
| yes                    | 2.95         | 4.8             | 12.9           |
| (512 * 4's average)    |              |                 |                |
# 2. throughput
- pool_size = 1GB
- total_cmds = 256 * 1024
## 2.1. H2C

| CPU | Bytes | Speed |     | GPU | Bytes | Speed |
| --- | :---: | :---: | --- | --- | :---: | :---: |
|     |  64   | 2.10  |     |     |  64   | 1.58  |
|     |  128  | 3.94  |     |     |  128  | 2.96  |
|     |  256  | 7.78  |     |     |  256  | 5.64  |
|     |  512  | 12.09 |     |     |  512  | 10.68 |
|     | 1024  | 12.14 |     |     | 1024  | 10.61 |

## 2.2. C2H

| CPU | Bytes | Speed |     | GPU | Bytes | Speed |
| --- | :---: | :---: | --- | --- | :---: | :---: |
|     |  64   | 4.97  |     |     |  64   | 4.97  |
|     |  128  | 9.93  |     |     |  128  | 9.93  |
|     |  256  | 11.91 |     |     |  256  | 11.92 |
|     |  512  | 12.19 |     |     |  512  | 13.00 |
|     | 1024  | 12.18 |     |     | 1024  | 13.00 |
# 3. random
- pool_size = 1GB
- total_cmds = 256 * 1024
## 3.1. H2C

| CPU | Bytes | Speed |  OPS   |     | GPU | Bytes | Speed |  OPS   |
| :-: | :---: | :---: | :----: | --- | --- | :---: | :---: | :----: |
|     |  64   | 2.01  | 33.732 |     |     |  64   | 1.52  | 25.472 |
|     |  128  | 3.98  | 33.417 |     |     |  128  | 2.92  | 24.535 |
|     |  256  | 7.30  | 30.629 |     |     |  256  | 5.82  | 24.400 |
|     |  512  | 12.00 | 25.176 |     |     |  512  | 10.48 | 21.971 |
|     | 1024  | 11.95 | 12.530 |     |     | 1024  | 10.63 | 11.151 |

## 3.2. C2H

| CPU | Bytes | Speed |  OPS   |     | GPU | Bytes | Speed |  OPS   |
| :-: | :---: | :---: | :----: | --- | --- | :---: | :---: | :----: |
|     |  64   | 4.97  | 83.333 |     |     |  64   | 4.97  | 83.332 |
|     |  128  | 9.90  | 83.035 |     |     |  128  | 9.93  | 83.332 |
|     |  256  | 11.68 | 48.991 |     |     |  256  | 11.92 | 50.000 |
|     |  512  | 11.76 | 24.660 |     |     |  512  | 13.00 | 27.269 |
|     | 1024  | 11.75 |  12.3  |     |     | 1024  | 13.00 | 13.633 |
# 4. latency
- pool_size = 1GB
- total_cmds = 256 * 1024
- Wait cycles: minimum cycles between two cmds
## 4.1. H2C

|   CPU   |   Bytes   |   Wait cycles   |   OPS limit (Mops)   |   Throughput (Mops)   |   Throughput (GB/s)   |   Latency (us)   |
| :-----: | :-------: | :-------------: | :------------------: | :-------------------: | :-------------------: | :--------------: |
|         |    64     |       50        |          5           |          4.6          |          0.3          |       1.17       |
|         |           |       25        |          10          |          8.8          |          0.6          |       1.12       |
|         |           |       12        |         20.8         |         17.0          |          1.1          |       1.16       |
|         |           |        6        |         41.7         |         29.8          |          1.9          |       1.17       |
|         |           |        0        |          -           |         31.6          |          2.0          |       3.15       |
|         |           |                 |                      |                       |                       |                  |
|         | 4 * 1024  |       100       |         2.5          |          2.3          |          9.3          |       1.60       |
|         |           |       90        |         2.8          |          2.6          |         10.4          |       1.62       |
|         |           |       85        |         2.9          |          2.7          |         11.0          |       1.67       |
|         |           |       80        |         3.1          |          2.9          |         11.6          |       1.91       |
|         |           |       75        |         3.3          |          3.1          |         12.3          |      13.99       |
|         |           |       70        |         3.6          |          3.1          |         12.3          |      14.16       |
|         |           |       50        |         5.0          |          3.1          |         12.3          |      14.33       |
|         |           |                 |                      |                       |                       |                  |
| **GPU** | **Bytes** | **Wait cycles** | **OPS limit (Mops)** | **Throughput (Mops)** | **Throughput (GB/s)** | **Latency (us)** |
|         |    64     |       50        |          5           |          4.6          |          0.3          |       1.23       |
|         |           |       25        |          10          |          8.8          |          0.6          |       1.22       |
|         |           |       12        |         20.8         |         17.0          |          1.1          |       1.23       |
|         |           |        6        |         41.7         |         29.8          |          1.9          |       1.21       |
|         |           |        0        |          -           |         30.4          |          1.9          |       3.30       |
|         |           |                 |                      |                       |                       |                  |
|         | 4 * 1024  |       100       |         2.5          |          2.3          |          9.3          |       2.94       |
|         |           |       90        |         2.8          |          2.6          |         10.2          |       3.28       |
|         |           |       85        |         2.9          |          2.6          |         10.6          |      16.38       |
|         |           |       80        |         3.1          |          2.6          |         10.6          |      16.51       |
|         |           |       75        |         3.3          |          2.6          |         10.6          |      16.53       |
|         |           |       70        |         3.6          |          2.6          |         10.6          |      16.56       |
|         |           |       50        |         5.0          |          2.6          |         10.6          |      16.65       |


## 4.2. C2H
- Latency CMD: duration between cmd issues and axibridge return
- Latency DATA: duration between last data issues and axibridge return
- * : this latency can be thousands us sometimes, because write latency use bridge channel to reply, single thread can issue around 8M bridge write, when ops exceeds this, the latency increase a lot.

|   CPU   |   Bytes   |   Wait cycles   |   OPS limit (Mops)   |   Throughput (Mops)   |   Throughput (GB/s)   |   Latency CMD (us)   |   Latency DATA (us)   |
| :-----: | :-------: | :-------------: | :------------------: | :-------------------: | :-------------------: | :------------------: | :-------------------: |
|         |    64     |       50        |          5           |          4.6          |         0.29          |         1.6          |          1.6          |
|         |           |       25        |          10          |          5.8          |         0.34          |          *           |           *           |
|         |           |                 |                      |                       |                       |                      |                       |
|         | 4 * 1024  |       100       |         2.5          |          2.3          |         9.35          |         1.5          |          1.2          |
|         |           |       90        |         2.8          |          2.6          |         10.37         |         1.5          |          1.2          |
|         |           |       85        |         2.9          |          2.7          |         10.96         |         1.6          |          1.3          |
|         |           |       80        |         3.1          |          2.9          |         11.63         |         1.7          |          1.4          |
|         |           |       75        |         3.3          |          3.0          |         12.17         |         11.3         |          6.9          |
|         |           |       70        |         3.6          |          3.0          |         12.17         |         11.3         |          6.9          |
|         |           |       50        |         5.0          |          3.0          |         12.17         |         11.4         |          7.0          |
|         |           |                 |                      |                       |                       |                      |                       |
| **GPU** | **Bytes** | **Wait cycles** | **OPS limit (Mops)** | **Throughput (Mops)** | **Throughput (GB/s)** | **Latency CMD (us)** | **Latency DATA (us)** |
|         |    64     |       50        |          5           |          0.9          |         0.06          |          *           |           *           |
|         |           |       25        |          10          |          0.9          |         0.06          |          *           |           *           |
|         |           |                 |                      |                       |                       |                      |                       |
|         | 4 * 1024  |       100       |         2.5          |          0.7          |         2.92          |          *           |           *           |
|         |           |       90        |         2.8          |          0.7          |         2.93          |          *           |           *           |
|         |           |       85        |         2.9          |          0.7          |         2.96          |          *           |           *           |
|         |           |       80        |         3.1          |          0.7          |         2.96          |          *           |           *           |
|         |           |       75        |         3.3          |          0.7          |         2.95          |          *           |           *           |
|         |           |       70        |         3.6          |          0.7          |         2.73          |          *           |           *           |
|         |           |       50        |         5.0          |          0.7          |         2.73          |          *           |           *           |

# 5. MMIO
- repeat_times = 16
- size = 1 * 1024 * 1024 (size of data a thread should write)

| theads num | speed    |
| ---------- | -------- |
| 1          | 0.263201 |
| 2          | 0.766281 |
| 4          | 1.035202 |
| 8          | 2.051150 |
| 16         | 3.851957 |

