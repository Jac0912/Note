CXL:（compute express link）：在 pcie 物理基础上的高性能互联协议，用于连接处理器、加速器、存储器等
# 1. 背景
![652](image/Pasted%20image%2020260620204814.png)
内存墙 & IO 墙问题：
- 硬件峰值计算提高 60000 倍
- 内存带宽提高 100 倍
- 互联带宽提高 30 倍
---

![517](image/Pasted%20image%2020260620203517.png)
CPU 中核的数量越来越多，每个核对应的内存通道带宽越来越小
当 CPU 所有内存通道占满后，只能通过再添加 CPU 来提高内存容量 -> 导致 CPU 计算能力未充分利用

---
CXL 出现：
- 扩展能力：DDR 通道有限、插槽有限
	```shell
	# 总带宽可以叠加 !!
		CPU
		├── DDR（本地）
		├── CXL设备1
		├── CXL设备2
		├── CXL设备3
	```
- 为 host 提供更大的内存
- 内存池化

# 2. Protocol
![419](image/Pasted%20image%2020260617175441.png)
- CXL.io: 约等于 pcie5.0 protocol，non-coherent load/store
- CXL.cache: 低延迟 cache host memory (request and response)
- CXL.mem: 直接使用 load/store 操作从设备的内存
三个协议的数据通过仲裁和多路复用模块一起动态复用，然后移交 PCIe5.0
# 3. CXL 设备
![953](image/Pasted%20image%2020260620215751.png)
## 3.1. Type1
CXL.io / CXL.cache，可以应用到网卡
- 可以无限进行原子操作
- type1 设备只需要小容量缓存（设备只是读取/修改）

## 3.2. Type2
GPU/FPGA 等，设备的内存可以为其他设备或主机访问，CXL 提供了将操作数写入内存并从设备中获取的方法，避免通过软件/硬件计算的方式获得
![622](image/Pasted%20image%2020260620221514.png)
两种偏置模式：
- 主机偏置
	- 主机：直接通过 DCOH 访问设备
	- 设备：CXL 设备需要先向主机发起 D2H 请求，主机将请求发送给设备中的 DCOH
- 设备偏置：设备执行工作时使用
	- 主机：仍然可以通过 ① 的方式去访问，但是可能强制放弃所有权
	- 设备：直接访问设备内存
## 3.3. Type3
用作主机内存扩展
- CXL.mem: 为主机的请求服务
- CXL.io: 设备发现、枚举等

# 4. CXL 组件
## 4.1. Flex bus
![512](image/Pasted%20image%2020260620172031.png) 
基于 pcie 物理层，实现 pcie/cxl 模式
通过链路训练来决定具体模式

## 4.2. Flit
Flit(Flexible-Length Interface Transport): 数据传输的基本单元
- 长度可变：根据数据大小动态调整
- 低延迟：数据包格式较短
- 灵活：根据需求携带不同的控制信息

## 4.3. DCOH
DCOH(device coherency): 设备一致性引擎
类似与把 CPU 的 MESI 扩展到设备

## 4.4. HDM
HDM(Host-Managed Device Memory): 由 CPU 来管理的设备内存，映射到系统的一致性空间并使用标准写回语义访问
外部内存不是由硬件的驱动管理，主机直接访问

## 4.5. 交换机
![509](image/Pasted%20image%2020260620174953.png)
共享内存（s1, s2, s3）：允许多个主机同时访问 CXL 的内存区域，保证每个主机缓存的都是一致性副本
是硬件层面的缓存一致性


# 5. Learn
## 5.1. MESI
![289](image/Pasted%20image%2020260620210524.png)
MESI 协议来解决缓存一致性问题
- M (Modeified): 该缓存行的内容已经修改，且该缓存行只缓存在该处理器内核中
- E (Exclusive): 该缓存行的内容未修改，且该缓存行被该处理器独占
- S (Shared):数据存在还存在与其他处理器内核的缓存中，但是缓存的数据与内存中一致
- I (Invalid): 该缓存行中的内容无效