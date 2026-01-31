# 1. Framework
## 1.1. overall
![|750](image/Pasted%20image%2020260130112705.png)
- SM(Streaming Multiprocessors): 矩阵乘
- Warp Scheduler: 向量运算单元
## 1.2. SM
![](image/Pasted%20image%2020260130151250.png)
- CUDA core(ALU): 多列，不同精度，可编程
	- 执行每个线程中的指令：可以通过 mask 使不同线程执行不同指令，每个线程可分配不同内存
	- 每个周期执行相同的算数运算：若一个 subpartition 中有一个 CUDA core 做浮点加法，这个 subpartition 都做浮点加法
## 1.3. Memory
- Registers: CUDA cores 使用
- SMEM(L1 Cache): 256KB 缓存单元，存储矩阵的 input ，可作为“shared memory”编程，后续芯片增加 TMEM 作为补充
- L2 Cache: 非编程掌控
- HBM: GPU 主要存储，存储模型梯度、weight

# 2. Networking
## 2.1. node
![](image/Pasted%20image%2020260130164515.png)
- NVLS: 对每个 H100 提供 hig-bandwidth 连接，每个 switch 提供多个 port 来建立多个 NVLink
- InfiniBand: 高速连接每个 node
## 2.2. beyond node
![](image/Pasted%20image%2020260130175551.png)
- SU(Scalable Units): 32 个 node
- SuperPod: 连接 SU，全连接 Leaf switch
# 3. Collectives Work
## 3.1. Intra-node collectives
**AllGather or ReduceScatter:**
![](https://jax-ml.github.io/scaling-book/assets/gpu/all-gather.gif)
$$
T_\text{AG or RS comms}=
\frac{\text{bytes}\times(N-1)}{N\times \text{GPU egress bandwidth}}
$$
**AllToAlls:**
$$
T_\text{AllToAll comms}=
\frac{B\times(N-1)}{W\times N^2}
$$
**top-k All to All:**
$$
\frac{N-1}{N}\times \min(\frac{k}{N},1) \times\frac{B}{W\times N}
$$
**In network reductions:**
可以在 switch 中直接做规约运算
![](image/Pasted%20image%2020260130213528.png)
$$
T_\text{SHARP AR comms}=
\frac{\text{bytes}}{ \text{GPU egress bandwidth}}
$$
## 3.2. Cross-node collectives

**node-level reduction**
$$
T_{\mathrm{AG} \text { or RS comms }}=\frac{\text { bytes }}{W_{\text {node egress }}} \underset{H 100}{=} \frac{\text { bytes }}{400 \mathrm{e} 9}
$$

**totle cost**
$$
T_{\text {total }}=\max \left(T_{\text {comms at node }}, T_{\text {comms in scale-out network }}\right)=\max \left[\frac{\text { bytes }}{W_{\text {GPU egress }}}, \frac{\text { bytes }}{W_{\text {node egress }}}\right]
$$

**N-way AllToAll**
spans: $M=N/8$
cost:
$$
T_{\text{AllToAll comms}}=\frac{B\cdot(M-1)}{M^2\cdot W_{\text{node egress}}}\approx\frac{B}{M\cdot W_{\text{node egress}}}
$$
**reductions(sharded over a separate axis)**
如果跨多个 node ，收益大
如果 node 内，收益小
$$
\begin{array}{c}
T_{\text {comms at node }}=\frac{\text { bytes }}{W_{\text {GPU egress }}} \cdot \frac{1}{\min \left(Y, D_{\text {node }}\right)} \\
T_{\text {comms in scale-out network }}=\frac{\text { bytes }}{W_{\text {node egress }}} \cdot \frac{D_{\text {node }}}{\max \left(D_{\text {node }}, Y\right)} \\
T_{\text {total }}=\max \left(T_{\text {comms at node }}, T_{\text {comms in scale-out network }}\right)
\end{array}
$$
