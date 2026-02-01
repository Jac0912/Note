# 1. Framework
## 1.1. overall
![|750](image/Pasted%20image%2020260130112705.png)
- SM(Streaming Multiprocessors): 矩阵乘
- Warp Scheduler: 向量运算单元，32 线程，起控制作用
## 1.2. SM
![](image/Pasted%20image%2020260130151250.png)
- CUDA core(ALU): 多列，不同精度，可编程
	- 执行每个线程中的指令：可以通过 mask 使不同线程执行不同指令，每个线程可分配不同内存
	- 每个周期执行相同的算数运算：若一个 subpartition 中有一个 CUDA core 做浮点加法，这个 subpartition 都做浮点加法
	- 操控 memory 中的数据更加灵活：shared register / thread state
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
### 3.1.1. AllGather or ReduceScatter
- AllGather: 每个 GPU 发出自己的一块数据，最终 GPU 拿到所有人的数据，拼成完整数组
- ReduceScatter: 所有 GPU 对各自副本（同一张量）做 reduction，然后每个 GPU 只拿回其中一块结果
ReduceScatter 的输入输出：
```text
输入：
GPU0: A0
GPU1: A1
GPU2: A2
GPU3: A3

->

输出：
GPU0: (A0+A1+A2+A3)[0]
GPU1: (A0+A1+A2+A3)[1]
GPU2: (A0+A1+A2+A3)[2]
GPU3: (A0+A1+A2+A3)[3]
```
![](https://jax-ml.github.io/scaling-book/assets/gpu/all-gather.gif)
$$
T_\text{AG or RS comms}=
\frac{\text{bytes}\times(N-1)}{N\times \text{GPU egress bandwidth}}
$$
### 3.1.2. AllToAlls
```text
GPU0: [a0 a1 a2 a3]
GPU1: [b0 b1 b2 b3]
GPU2: [c0 c1 c2 c3]
GPU3: [d0 d1 d2 d3]

->

GPU0: [a0 b0 c0 d0]
GPU1: [a1 b1 c1 d1]
GPU2: [a2 b2 c2 d2]
GPU3: [a3 b3 c3 d3]
```
$$
T_\text{AllToAll comms}=
\frac{B\times(N-1)}{W\times N^2}
$$
### 3.1.3. top-k All to All 
MoE 模型：
- token: 模型一次处理的最小输入单元
- expert: MoE 中的一个子网络模块
- 每个 token 会送到其中第 k 个 expert
- 可能是稀疏的：当 $k<N$ 的时候带来好处
- 实际上对 non-zero 采样是随机的：有 $(N-1)/N$ 的概率 token 需要走网络
$$
\frac{N-1}{N}\times \min(\frac{k}{N},1) \times\frac{B}{W\times N}
$$
### 3.1.4. In network reductions
可以在 switch 中直接做规约运算
![](image/Pasted%20image%2020260130213528.png)
AllReduce 输入输出：
```text
输入：
GPU0: A0
GPU1: A1
GPU2: A2
GPU3: A3

—>

输出：
GPU0: A0 + A1 + A2 + A3
GPU1: A0 + A1 + A2 + A3
GPU2: A0 + A1 + A2 + A3
GPU3: A0 + A1 + A2 + A3
```
$$
T_\text{SHARP AR comms}=
\frac{\text{bytes}}{ \text{GPU egress bandwidth}}
$$
流程：
1. GPU egresses: $B\cdot (N-1)/N$
2. GPU ingress reduced version of **its** local shard: $B/N$
3. GPU egresses: $B/N$
4. GPU ingresses fully reduced result: $B\cdot (N-1)/N$
## 3.2. Cross-node collectives
### 3.2.1. node-level AllGather or ReductionScatter (roughly)
$$
T_{\mathrm{AG} \text { or RS comms }}=\frac{\text { bytes }}{W_{\text {node egress }}} \underset{H 100}{=} \frac{\text { bytes }}{400 \mathrm{e} 9}
$$

**totle cost (roughly):**
$$
T_{\text {total }}=\max \left(T_{\text {comms at node }}, T_{\text {comms in scale-out network }}\right)=\max \left[\frac{\text { bytes }}{W_{\text {GPU egress }}}, \frac{\text { bytes }}{W_{\text {node egress }}}\right]
$$

**presice:**
$$
T_{\text{AG or RS comms}}=\mathrm{bytes}\cdot max_\mathrm{depth~i}\left[\frac{D_i-1}{D_i\cdot W_\mathrm{link~i}}\right]
$$
### 3.2.2. N-way AllToAll
不太相同，因为 AllToAll 不是分层的
spans: $M=N/8$ nodes
cost:
$$
T_{\text{AllToAll comms}}=\frac{B\cdot(M-1)}{M^2\cdot W_{\text{node egress}}}\approx\frac{B}{M\cdot W_{\text{node egress}}}
$$
### 3.2.3. reductions(sharded over a separate axis)
**targt:**
$$
\mathrm{AllReduce}_{X}(A[I_{Y},J]\left\{U_{X}\right\})
$$
- $\mathrm{AllReduce}_{X}(A[I_{Y},J]$：按第一个维度 Y 切分
- $\left\{U_{X}\right\}$：在 X 轴上 unsharded / replicated
**comms(若 Y 跨多个 node):**
$$
\begin{array}{c}
T_{\text {comms at node }}=\frac{\text { bytes }}{W_{\text {GPU egress }}} \cdot \frac{1}{\min \left(Y, D_{\text {node }}\right)} \\
T_{\text {comms in scale-out network }}=\frac{\text { bytes }}{W_{\text {node egress }}} \cdot \frac{D_{\text {node }}}{\max \left(D_{\text {node }}, Y\right)} \\
T_{\text {total }}=\max \left(T_{\text {comms at node }}, T_{\text {comms in scale-out network }}\right)
\end{array}
$$
- $D_{\text {node }}$: 一个 node 中的 GPU 数目
- if $Y<D_{\text {node }}$: node 层可以省时间，总时间不变
- if $Y>D_{\text {node }}$: 得到的加速与跨越的 node 成正比
### 3.2.4. AllGather
**targt:**
$$
\mathrm{AllGather}_{X}(A_{Y}\left\{U_{X}\right\})
$$
**comms:**
$$
T_{\text{AR or RS comms}}=\mathrm{bytes}\cdot\max_{\mathrm{depth~}i}\left[\frac{D_{i}-1}{D_{i}\cdot\max(Y,S_{i-1})\cdot W_{\mathrm{link~}i}}\right]
$$
- $S_i: M\times N\dots$：第 i 层下子节点的 size（i 节点下的 GPU 数量）
# 4. Rooflines for LLM
**target:**
$$
\mathrm{MLP}(x)\equiv x[B,D]*_{D}W_{\mathrm{in}}[D,F]\cdot_{F}W_{\mathrm{out}}[F,D]
$$
- MLP: $\text{Linear}(D\rightarrow F)\rightarrow \text{Activation} \rightarrow \text{Linear} (F\rightarrow D)$
## 4.1. Data Parallelism
- 每个 GPU 有完整的模型副本，处理不同的 batch
- 反向梯度计算完后，通过 AllReduce 更新模型参数
- 通信带宽压力大
### 4.1.1. MLP
$$
\begin{gathered}
T_{\mathrm{math}}=\frac{2\cdot2\cdot2\cdot BDF}{X\cdot C} \\
T_{\mathrm{comms}}=\frac{2\cdot2\cdot2\cdot DF}{W_{\text{collective}}} \\
\text{Therefore, for }T_{\mathrm{math}}>T_{\mathrm{comms}},\text{we need }B/(XC)>1/W_{\text{collective}}\mathrm{~or} \\
\frac{B}{X}>\frac{C}{W_{\text{collective}}}
\end{gathered}
$$
- B: batch size
- X: 并行程度
- C: GPU 计算吞吐量
- D: 模型宽度
- F: 中间维度
- $2\cdot2\cdot2$
	- $T_\text{math}$
		- forward + backward
		- D -> F, F -> D
		- 两次矩阵乘
	- $T_\text{comms}$
		- 两层权重矩阵
		- forward + backward 的梯度
		- AllReduce = ReduceScatter + AllGather
### 4.1.2. MoE
- Mixture of Experts(MoE): E experts and k experts per token
$$
\begin{aligned}
 & T_{\mathrm{math}}=\frac{2\cdot2\cdot2\cdot k\cdot BDF}{X\cdot C} \\
 & T_{\mathrm{comms}}=\frac{2\cdot2\cdot2\cdot EDF}{W_{\text{collective}}} \\
 & \rightarrow \\
 & \frac{B}{X}>\frac{E}{k}\frac{C}{W_{\text{collective}}}
\end{aligned}
$$
### 4.1.3. The number of nodes is small

$$
\begin{aligned}
T_{\mathrm{math}} & =\frac{2\cdot2\cdot2\cdot BDF}{N*C} \\
T_{\mathrm{comms}} & =\frac{2\cdot2\cdot2\cdot DF\cdot(X-1)}{X\cdot W_{\text{collective}}}
\end{aligned}
$$
- X: node 的数量
- N = 8 * X
- X = 2 时效果好，可以显著减少通信量

## 4.2. Tensor Parallelism
- 模型拆分到不同 GPU，每个 GPU 负责一部分计算
- 前向反向传播需要交换激活值
- 通信量小，需要频繁通信

$$
\begin{aligned}
 & T_{\mathrm{math}}=\frac{2\cdot2\cdot BDF}{Y\cdot C} \\
 & T_{\mathrm{comms}}=\frac{2\cdot2\cdot BD}{W_{\text{collective}}} \\
\rightarrow & Y<\frac{F\cdot W_{\text{collective}}}{C}
\end{aligned}
$$
- feed-forward dimension F 是 communication-bound
## 4.3. Expert Parallelism
- 不同专家分布到不同 GPU
- 每个 token 只计算部分专家
MoE 通过仅提高 k 倍 FLOPs 来提高 E 倍模型权重，使得数据并行困难，可以通过 expert 维度共享权重: $W_{in}[E_Z,D,F]$
$$
\begin{aligned}
T_{\mathrm{AllToAll}}&=2\cdot B\cdot D\cdot(Z-8)/Z\min(8*k/Z,1)\\
\rightarrow & \\
T_{\mathrm{math}} &= \frac{4\cdot B\cdot k\cdot D\cdot F}{Z\cdot C} \\
T_{\mathrm{comms}}&=\frac{4\cdot B\cdot D\cdot(Z-8)}{W\cdot Z}\cdot\min\left(\frac{8\cdot k}{Z},1\right) \\
\end{aligned}
$$
所以需要：
- F 小的时候：并行规模越大，需要更大的 FFN(F)规模来提高计算密度
$$
K>Z/8\mathrm{~with~}F>\alpha\cdot(Z-8)/k 
$$
或者：
- F 大的时候：Z 的规模可以到很大，不容易被 F 限制
$$
Z\gg K\mathrm{~and~}F>8\cdot\alpha\mathrm{,~where} \:\alpha =C/W
$$
## 4.4. Pipeline Parallelism
- 模型按层切分，每个 GPU 负责一段连续层
- 数据 batch 在层间流动
成本很低：前向反向传播的时候只是在相邻的节点间来通行，但是会出现 pipline bubbles（后几个 GPU 需要在流水线上等待前几个 GPU 传递数据），可以通过 zero-bubble 解决（反向正向传播同时进行）
- microbatches: $N_{MB}$
- $T_\text{comms per hop}=2\cdot B\cdot D/(W\cdot N_{MB})$
- hops: $N_{MB}+N_\text{stages}-2$
$$
\begin{aligned}
T_{\text{total PP comms}} & =\frac{2BD}{W\cdot N_{\mathrm{MB}}}\cdot(N_{\mathrm{MB}}+N_{\mathrm{stages}}-2) \\
 \\
T_{\text{per-layer comms}} & \approx1.5\cdot\frac{2BD}{W\cdot N_{\mathrm{layers}}}
\end{aligned}
$$
由于是按层划分，pipline parallelism 的成本应该很低，为什么不应用流水并行：
- 代码复杂：流水优化不能很好嵌入并行框架，前向反向传播交叉复杂
- 数据并行和 FSDP 困难：每个 microbatch 都要 AllGather，通信压力大
	- Fully Sharded Data Parallel(FSDP): 完全分片数据并行
	- ![](image/Pasted%20image%2020260201171003.png)
	- 梯度同步需要等到最后一个 microbatch 完成后才能开始
- pipline bubbles and step imbalance: 由于传播时需要传递 activations，会造成额外开销
	- ![](image/Pasted%20image%2020260201171908.png)

| 级别     | 分片内容        |
| ------ | ----------- |
| ZeRO-1 | 优化器状态       |
| ZeRO-2 | + 梯度        |
| ZeRO-3 | + 模型参数（最激进） |
