```
PCIE4C_X1Y0
//old
create_ip -name qdma -vendor xilinx.com -library ip -version 4.0 -module_name QDMABlackBox
set_property -dict [list CONFIG.Component_Name {QDMABlackBox} CONFIG.mode_selection {Advanced} CONFIG.axist_bypass_en {true} CONFIG.pcie_extended_tag {false} CONFIG.pcie_blk_locn {X1Y2} CONFIG.dsc_byp_mode {Descriptor_bypass_and_internal} CONFIG.pl_link_cap_max_link_speed {8.0_GT/s} CONFIG.pl_link_cap_max_link_width {X16} CONFIG.cfg_mgmt_if {false} CONFIG.testname {st} CONFIG.pf0_bar4_enabled_qdma {true} CONFIG.pf0_bar4_64bit_qdma {true} CONFIG.pf0_bar4_scale_qdma {Megabytes} CONFIG.pf0_bar4_size_qdma {256} CONFIG.dma_intf_sel_qdma {AXI_MM_and_AXI_Stream_with_Completion} CONFIG.en_axi_mm_qdma {false}] [get_ips QDMABlackBox]

set_property -dict [list CONFIG.Component_Name {QDMABlackBox} CONFIG.mode_selection {Advanced} CONFIG.axist_bypass_en {true} CONFIG.pcie_extended_tag {false} CONFIG.pcie_blk_locn {PCIE4C_X1Y0} CONFIG.dsc_byp_mode {Descriptor_bypass_and_internal} CONFIG.pl_link_cap_max_link_speed {8.0_GT/s} CONFIG.pl_link_cap_max_link_width {X16} CONFIG.en_bridge_slv {true} CONFIG.csr_axilite_slave {false} CONFIG.axibar_notranslate {true} CONFIG.cfg_mgmt_if {false} CONFIG.testname {st} CONFIG.pf0_bar4_enabled_qdma {true} CONFIG.pf0_bar4_64bit_qdma {true} CONFIG.pf0_bar4_scale_qdma {Megabytes} CONFIG.pf0_bar4_size_qdma {256} CONFIG.dma_intf_sel_qdma {AXI_MM_and_AXI_Stream_with_Completion} CONFIG.en_axi_mm_qdma {false}] [get_ips QDMABlackBox]

//slave-old
set_property -dict [list CONFIG.Component_Name {QDMABlackBox} CONFIG.mode_selection {Advanced} CONFIG.axist_bypass_en {true} CONFIG.pcie_extended_tag {false} CONFIG.pcie_blk_locn {X1Y2} CONFIG.dsc_byp_mode {Descriptor_bypass_and_internal} CONFIG.pl_link_cap_max_link_speed {8.0_GT/s} CONFIG.pl_link_cap_max_link_width {X16} CONFIG.en_bridge_slv {true} CONFIG.csr_axilite_slave {false} CONFIG.axibar_notranslate {true} CONFIG.cfg_mgmt_if {false} CONFIG.testname {st} CONFIG.pf0_bar4_enabled_qdma {true} CONFIG.pf0_bar4_64bit_qdma {true} CONFIG.pf0_bar4_scale_qdma {Megabytes} CONFIG.pf0_bar4_size_qdma {256} CONFIG.dma_intf_sel_qdma {AXI_MM_and_AXI_Stream_with_Completion} CONFIG.en_axi_mm_qdma {false}] [get_ips QDMABlackBox]
```

# 1. throughput
- pool_size = 1GB
- total_cmds = 256 * 1024
## 1.1. H2C

| CPU | Bytes | Speed |     | GPU | Bytes | Speed |
| --- | :---: | :---: | --- | --- | :---: | :---: |
|     |  64   | 1.05  |     |     |  64   | 2.24  |
|     |  128  | 2.00  |     |     |  128  | 3.72  |
|     |  256  | 3.91  |     |     |  256  | 6.42  |
|     |  512  | 6.20  |     |     |  512  | 6.19  |
|     | 1024  | 6.09  |     |     | 1024  | 6.30  |

## 1.2. C2H

| CPU | Bytes | Speed |     | GPU | Bytes | Speed |
| --- | :---: | :---: | --- | --- | :---: | :---: |
|     |  64   | 2.71  |     |     |  64   | 2.71  |
|     |  128  | 4.95  |     |     |  128  | 4.97  |
|     |  256  | 6.08  |     |     |  256  | 6.50  |
|     |  512  | 6.08  |     |     |  512  | 6.50  |
|     | 1024  | 6.09  |     |     | 1024  | 6.50  |
# 2. random
- pool_size = 1GB
- total_cmds = 256 * 1024
## 2.1. H2C

| CPU | Bytes | Speed |  OPS   |     | GPU | Bytes | Speed |  OPS   |
| :-: | :---: | :---: | :----: | --- | --- | :---: | :---: | :----: |
|     |  64   | 1.03  | 17.285 |     |     |  64   | 0.64  | 10.662 |
|     |  128  | 1.99  | 16.722 |     |     |  128  | 1.31  | 10.983 |
|     |  256  | 3.72  | 15.616 |     |     |  256  | 2.36  | 9.886  |
|     |  512  | 6.04  | 12.945 |     |     |  512  | 4.72  | 9.892  |
|     | 1024  | 6.18  | 6.479  |     |     | 1024  | 4.51  | 4.731  |

## 2.2. C2H

| CPU | Bytes | Speed |  OPS   |     | GPU | Bytes | Speed |  OPS   |
| :-: | :---: | :---: | :----: | --- | --- | :---: | :---: | :----: |
|     |  64   | 2.71  | 45.487 |     |     |  64   | 2.71  | 45.547 |
|     |  128  | 4.97  | 41.652 |     |     |  128  | 4.97  | 41.708 |
|     |  256  | 5.92  | 24.823 |     |     |  256  | 6.50  | 27.276 |
|     |  512  | 5.90  | 12.363 |     |     |  512  | 6.50  | 13.634 |
|     | 1024  | 5.93  | 6.221  |     |     | 1024  | 6.50  | 6.816  |
# 3. latency
- pool_size = 1GB
- total_cmds = 256 * 1024
- Wait cycles: minimum cycles between two cmds
## 3.1. H2C

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


## 3.2. C2H
- Latency CMD: duration between cmd issues and axibridge return
- Latency DATA: duration between last data issues and axibridge return
- \* : this latency can be thousands us sometimes, because write latency use bridge channel to reply, single thread can issue around 8M bridge write, when ops exceeds this, the latency increase a lot.

|            CPU            |   Bytes   |   Wait cycles   |   OPS limit (Mops)   |   Throughput (Mops)   |   Throughput (GB/s)   |   Latency CMD (us)   |   Latency DATA (us)   |
| :-----------------------: | :-------: | :-------------: | :------------------: | :-------------------: | :-------------------: | :------------------: | :-------------------: |
|                           |    64     |       50        |          5           |          4.6          |         0.29          |         1.6          |          1.6          |
|                           |           |       25        |          10          |          5.8          |         0.34          |          *           |           *           |
|                           |           |                 |                      |                       |                       |                      |                       |
|                           | 4 * 1024  |       100       |         2.5          |          2.3          |         9.35          |         1.5          |          1.2          |
|                           |           |       90        |         2.8          |          2.6          |         10.37         |         1.5          |          1.2          |
|                           |           |       85        |         2.9          |          2.7          |         10.96         |         1.6          |          1.3          |
|                           |           |       80        |         3.1          |          2.9          |         11.63         |         1.7          |          1.4          |
|                           |           |       75        |         3.3          |          3.0          |         12.17         |         11.3         |          6.9          |
|                           |           |       70        |         3.6          |          3.0          |         12.17         |         11.3         |          6.9          |
|                           |           |       50        |         5.0          |          3.0          |         12.17         |         11.4         |          7.0          |
|                           |           |                 |                      |                       |                       |                      |                       |
|          **GPU**          | **Bytes** | **Wait cycles** | **OPS limit (Mops)** | **Throughput (Mops)** | **Throughput (GB/s)** | **Latency CMD (us)** | **Latency DATA (us)** |
|                           |    64     |       50        |          5           |          0.9          |         0.06          |          *           |           *           |
|                           |           |       25        |          10          |          0.9          |         0.06          |          *           |           *           |
|                           |           |                 |                      |                       |                       |                      |                       |
|                           | 4 * 1024  |       100       |         2.5          |          0.7          |         2.92          |          *           |           *           |
|                           |           |       90        |         2.8          |          0.7          |         2.93          |          *           |           *           |
|                           |           |       85        |         2.9          |          0.7          |         2.96          |          *           |           *           |
|                           |           |       80        |         3.1          |          0.7          |         2.96          |          *           |           *           |
|                           |           |       75        |         3.3          |          0.7          |         2.95          |          *           |           *           |
|                           |           |       70        |         3.6          |          0.7          |         2.73          |          *           |           *           |
|                           |           |       50        |         5.0          |          0.7          |         2.73          |          *           |           *           |
|                           |           |                 |                      |                       |                       |                      |                       |
| **total cmd: 64 \* 1024** | 16 * 1024 |      2500       |         0.1          |          0.1          |         1.52          |         6.0          |          5.0          |
|                           |           |      2000       |         0.1          |          0.1          |         1.91          |         6.0          |          5.0          |
|                           |           |      1500       |         0.2          |          0.2          |         2.54          |         9.8          |          8.7          |
|                           |           |       500       |         0.5          |          0.5          |         7.60          |         13.8         |         12.7          |
|                           |           |       400       |         0.6          |          0.6          |         9.49          |         20.4         |         19.3          |
|                           |           |       300       |         0.8          |          0.8          |         12.63         |         24.7         |         23.6          |
|                           |           |       250       |         1.0          |          0.8          |         12.94         |         34.4         |         27.4          |

```

```

# 4. MMIO
- repeat_times = 100
- size = 1 * 1024 * 1024 (size of data a thread should write)
## 4.1. cpu

| Bytes                   | 1        | 2        | 3        | 4        | 8        | 16       | 32       |
| ----------------------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
| 1 \* 16 \* 1024 \* 1024 | 0.577843 | 1.174964 | 1.269347 | 1.510956 | 3.183329 | 4.444106 | 4.422952 |
| 1 \* 4 \* 1024 \* 1024  | 0.517343 | 0.731896 | 1.071978 | 1.475690 | 2.950375 | 4.384705 | 4.706933 |
| 1 \* 1 \* 256 \* 1024   | 0.285686 | 0.541761 | 0.864995 | 1.069133 | 2.411720 | 4.082604 | 4.676021 |
| 1 \* 1 \* 64 \* 1024    | 0.258058 | 0.990815 | 0.743837 | 1.023322 | 2.219052 | 3.846412 | 4.490995 |
| 1 \* 1 \* 16 \* 1024    | 0.288854 | 0.787825 | 0.994746 | 0.950602 | 1.855375 | 3.470068 | 3.941046 |

## 4.2. gpu

| Wraps                    | 1    | 2    | 3    | 4    | 8    | 16   |
| ------------------------ | ---- | ---- | ---- | ---- | ---- | ---- |
| 1 \* 256 \* 1024 \* 1024 | 8.42 | 8.96 | 8.96 | 8.96 | 8.96 | 8.96 |
| 1 \* 64 \* 1024 \* 1024  | 7.84 | 8.95 | 8.95 | 8.95 | 8.95 | 8.95 |
| 1 \* 16 \* 1024 \* 1024  | 5.88 | 8.94 | 8.94 | 8.94 | 8.94 | 8.94 |
| 1 \* 4 \* 1024 \* 1024   | 5.32 | 8.85 | 8.85 | 8.88 | 8.90 | 8.90 |
| 1 \* 1 \* 256 \* 1024    | 4.37 | 7.47 | 7.62 | 7.53 | 7.56 | 7.62 |


```

```