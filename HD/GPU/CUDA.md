# 1. common
```bash
# 查看架构名
nvidia-smi -q | grep Architecture

# 查看计算能力
nvidia-smi --query-gpu=compute_cap --format=csv,noheader
```
# 2. [install](https://developer.nvidia.com/cuda/toolkit)

```bash
# .bashrc
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-13.2/lib64
export PATH=$PATH:/usr/local/cuda-13.2/bin
export CUDA_HOME=$CUDA_HOME:/usr/local/cuda-13.2
export PATH=/usr/local/cuda/bin:$PATH
```

# 3. 线程模型
## 3.1. 概念
![|375](image/Pasted%20image%2020260408203130.png)
- grid：网格，CPU 启动核函数，所有的线程构成 grid
- block：线程块，包含多个线程
- 配置线程：`<<<grid_size, block_size>>>`
- 多维线程模型：
	```cpp
	gridDim.x  //max: 2^31 -1
	gridDim.y  //max: 2^16 -1
	gridDim.z  //max: 2^16 -1
	
	// 线程块总大小最大为 1024 !!
	blockDim.x  //max: 1024
	blockDim.y  //max: 1024
	blockDim.z  //max: 64
	
	// 定义一维
	<<<grid_size, block_size>>>
	// 定义多维，后面两维为 1 的时候可以省略
	dim3 grid_size(Gx, Gy, Gz);
	dim3 block_size(Bx, By, Bz);
	```
	- `gridDim` 和 ` blockDim` 均为内建变量
	- 只能在核函数使用，无需定义
	- 最多支持三维
	- 每个线程都有唯一的 id，可以通过 `gridDim` 和 ` blockDim` 计算
## 3.2. 线程 id 计算
- 一维 grid & 一维 block
	`int id = blockIdx.x * blockDim.x + threadIdx.x`
- 二维 grid & 二维 block
	```cpp
	int blockId = blockIdx.x + blockIdx.y * gridDim.x;
	int threadId = threadIdx.y * blockDim.x + threadIdx.x;
	int id = blockId * (blockDim.x * blockDim.y) + threadId;
	```
- 三维 grid & 三维 block
	```cpp
	int blockId = gridDim.x * gridDim.y * blockIdx.z
				 + blockIdx.x + blockIdx.y * gridDim.x;
	int threadId = blockDim.x * blockDim.y * threadIdx.z
 				 + threadIdx.x + threadIdx.y * blockDim.x;
 	int id = blockId * (blockDim.x * blockDim.y * blockDim.z)
 			+ threadId
	```

## 3.3. 物理结构
- 线程模型可以定义成千上万个线程
- grid 中所有线程块需要分配到 SM 上进行
- 线程块中所有线程分配到同一个 SM 中执行，每个 SM 中可以分配多个线程块
- 线程块分配到 SM 中后，**32** 个线程为一组进行分割，每个组成一个 wrap（线程束）
- wrap 做到硬件上的并行
# 4. nvcc 编译
## 4.1. 编译流程
1. nvcc 分离源码：(1) 主机代码 (2) 设备代码
2. 主机代码为 cpp，设备代码为扩展代码
3. nvcc 将设备代码编译为 PTX（Parallel Thread Execution）伪汇编代码，再将 PTX 编译成二进制 cubin 代码
4. 编译为 PTX 时，需要用选项 `-arch=compute_XY` 指定虚拟架构计算能力，来确定代码中能够使用的 CUDA 功能
	- X：主版本号（代码不能跨版本，架构差异大）
	- Y：次版本号（代码相互兼容，性能差异）
5. 编译成 cubin 代码时，需要用选项 `-code=sm_ZW` 指定真实架构的计算能力，用以确定可执行文件能够使用的 GPU

## 4.2. PTX
- 是 CUDA 平台为基于 GPU 通用计算定义的虚拟机和指令集
- nvcc 使用两个体系结构：虚拟中间结构、GPU 体系结构
- 虚拟架构尽可能低——适配更多实际 GPU
- 真实架构尽可能高——充分发挥 GPU 性能
**指令的并行程度**
## 4.3. nvcc 即时编译
- 可执行文件中保留 PTX 代码，如果平台不匹配通过 PTX 代码编译出一份 cubin 文件
- `-gencode arch=compute_XY,code=compute_XY`
- 两个计算能力都是虚拟架构的计算能力（类似于 tag）
- 两个虚拟架构计算能力需要一致

## 4.4. Note
- 二进制 cubin 代码，大版本之间不兼容
- 指定真实架构能力，必须指定虚拟架构计算能力
- 真实架构能力 >= 虚拟架构能力
- 低小版本到高小版本兼容
- 可以指定多个 `-arch / -code`，编译成 fat 二进制文件，来适配多平台

# 5. 编程
## 5.1. 程序框架
- 核函数
- main
	1. 设置 GPU 设备
	2. 分配主机、设备内存
	3. 初始化主机数据
	4. 数据从主机复制到设备
	5. 调用核函数在设备中计算
	6. 将计算数据传给主机
	7. 释放主机、设备内存
## 5.2. 内存管理
### 5.2.1. 全局内存

- 动态全局内存
	
	| standard c functions | cuda c functions |
	| :------------------: | :--------------: |
	|        malloc        |    cudaMalloc    |
	|        memcpy        |    cudaMecpy     |
	|        memset        |    cudaMemset    |
	|         free         |     cudaFree     |
- 静态全局内存
	- `__device__` 修饰
	- host / device / global 函数外部定义
	- 核函数可以直接调用（不用传参）
	- 主机不能直接调用，通过 api 通信（`cudaMemcpyToSymbol` / `cudaMemcpyFromSymbol`）
### 5.2.2. 常量内存
- `__constant__` 修饰
- 不能定义在函数中，静态定义
- 核函数传参的变量存放于常量内存
- 适用于 wrap 中所有线程从相同地址读数据，每个 wrap 读取一次，广播到所有线程
- 主机不能直接调用，通过 api 通信（`cudaMemcpyToSymbol` / `cudaMemcpyFromSymbol`）
### 5.2.3. 共享内存
- 声明后，每个线程块都会创建相同大小的共享内存
- 动态共享内存
	- `extern __shared__ float array[]`
	- `extern` 是必须的，声明数组必须使用 `[]`
	- `<<<grid, block, num>>>`, `num` 是动态共享内存的大小
- 静态共享内存
	- `__shared__` 修饰
	- 作用域
		- 核函数中声明：局限于核函数
		- 核函数外，对所有核函数有效
	- 编译时确定大小
- Note:
	- 需要加入同步机制（`__syncthreads()`，保证同一线程块中的线程同步到确定的点）

### 5.2.4. 缓存
- 默认不开启 L1
	- 查询是否有 L1：``
	- 通过编译指令开启：
		- `-Xptxax -dlcm=ca`：除了带禁用缓存修饰符的数据，所有读取都缓存
		- `-Xptxax -fscm=ca`：所有数据读取都缓存

## 5.3. 函数
- 设备函数
	- 定义只能在 GPU 设备上运行的函数
	- 只能被其它核函数或其它设备函数调用
	- 用 `__device__` 修饰
- 核函数
	- `__global__` 修饰，主机调用，设备执行
	- 不可与 `__host__` / `__device__` 同时使用
	- 返回值为 `void`
	- 核函数只能访问 GPU 内存
	- 核函数不使用变长参数
	- 核函数不使用函数指针
	- 核函数异步
- 主机函数
	- 主机端普通 cpp 函数可用 `__host__` 修饰
	- 主机端函数 `__host__` 修饰可以省略
	- `__host__` 和 `__device__` 可同时修饰一个函数，编译器会针对主机和设备分别编译该函数


4.4 cmake