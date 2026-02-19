**传统数据处理：**
1. CPU 与 NIC 协调通过 GPUDirect RDAM 来接受 GPU 内存包
2. CPU 通知 GPU 上的 CUDA 来处理包
![|550](image/Pasted%20image%2020260201230842.png)
**DOCA GPUNetIO 技术：**
- GPUDirect RDMA：NIC 和 GPU 直传包
- GPUDirect Async Kernel-Initiated (GDAKI)：GPU CUDA 核心，来控制网络通信发出或接收数据
	- 支持：Ethernet (Ethernet/IP/UDP/TCP/ICMP)、RDMA 通信(InfiniBand, RoCE)
	- IBGDA：与 InfiniBand 一起使用的别称
- GDRCopy Library: CPU 直接获得 GPU 内存
- NVIDIA BlueField DMA Engine：支持 GPU-triggered 内存复制
![|575](image/Pasted%20image%2020260201230945.png)
```shell
sudo docker run -v <source-code-folder>:/doca_devel -v /dev/hugepages:/dev/hugepages --privileged --net=host -it <image-name/ID>

sudo docker run -v /home/jac0912/GPUProjects/learn:/doca_devel -v /dev/hugepages:/dev/hugepages --gpus all  --pr
ivileged --net=host -it d6270c71eaf3

> curl -L https://repo1.maven.org/maven2/com/lihaoyi/mill-dist/0.12.8/mill-dist-0.12.8-mill.sh -o mill
> chmod +x mill
> echo 0.12.17 > .mill-version
```

