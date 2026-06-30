全同态加密（Fully Homomorphic Encryption, FHE）：允许数据保持加密状态下直接进行任意计算，计算完成后解密得到的结果与先解密再计算一致
- BGV / BFV：整数域上精确计算，安全投票、加密数据库查询
- TFHE：针对布尔电路优化，频繁条件判断的场景
- CKKS（Cheon-Kim-Kim-Song）：
	- 实现对**浮点数据**密态算数的能力
	- 允许可控误差换性能提升，机器学习推理、信号处理、统计分析

# 1. CKKS
- 客户端：
	- 持有：secret key / public key
	- 负责：packing / encoding / encryption
	- 对结果：decryption / decoding
- 云服务器：
	- 负责：homomorphic operations
## 1.1. 分圆多项式商环
$$
R_q = \mathbb{Z}_q[X]/(X^N+1)
$$
- $\mathbb{Z}_q[X]$：系数属于模 q 整数域所有的多项式
	- eg. $20X^2 = 3X^2$
- $/(X^N+1)$：所有次数超过 N - 1 的项用 $X^N = -1$ 化简
	- 环中始终有 N 个系数
- N 需要是 2 的幂：使用 NTT[^1]
- $q \equiv 1 \pmod{2N}$：NTT 需要 2N 次本原单位根（2N 等分一个圆，取与 2N 互质的单位根，共 N 个）
- 商环 $R_q$ 中任意多项式可同构到 $\mathbb{C}^{N/2}$（把一个多项式转换成多个复数槽位(slots))
	- 每个根两两共轭，所有只有 N/2 个
	- 一个密文能处理 N/2 个数据，实现 SIMD（Single Instrucion Multiple Data）

## 1.2. packing
对待编码的复数向量
$$
\begin{equation}
	\mathbf{z} = (z_0, \dots，z_{n-1}) \in \mathbb{C}^n, \quad n \leq N/2
\end{equation}
$$
打包到长度为 **N/2**（N 大小的环可以加密长度为 N/2 的向量） 的向量中，多余槽位用 0 填充，后续密文计算等价于在打包向量上执行的明文运算
## 1.3. encoding
表示浮点数据
使用缩放因子（scaling factor）$\Delta > 0$ 将浮点数坐标缩放并舍入到整数表示
编码步骤：
1. 利用映射 $\pi ^{-1}(\mathbf{z})$ 来扩展 $\mathbf{z}$ 为 N 项
2. 每个槽位乘缩放因子，并缩放到整数范围（保证精度）：
$$
\begin{equation}
\hat{\mathbf{z}} = \Delta \cdot \tilde{\mathbf{z}}
\end{equation}
$$
3. 利用负循环反傅里叶变换（negacyclic Inverse Fast Fourier Transform, negacyclic IFFT）将 $\hat{\mathbf{z}}$ 映射为负系数多项式 $P(X) \in \mathbb{C}[X]/(X^N+1)$ （求解该多项式），满足插值性质：在 $2N$ 次本原单位根 $\xi = e^{2\pi i/(2N)}$ 的奇次幂 $\xi， \xi^3， \dots， \xi^{2N-1}$ 处求值时，可近似还原原始的缩放向量，即：
$$
\begin{equation}
	\tilde{\mathbf{z}} \approx (P(\xi),P(\xi^3),\dots,P(\xi^{2N-1}))
\end{equation}
$$
4. 对每个系数执行坐标独立（coordinate-wise）的随机舍入（randomized rouding），得到整系数多项式：$m(X) \in R_q = \mathbb{Z}_q[X]/(X^N+1)$
---

最大坐标误差界：
$$
\begin{equation}
	\lVert \mathbf{z} - \mathbf{z}' \rVert_\infty \lesssim \frac{N}{\pi\Delta}
\end{equation}
$$

## 1.4. 密钥体系
- 密钥：记为 $\mathsf{sk}=(1，s)$，其中 $s(X) \in R_Q$ 的系数从小分布(如三值分布 $\{-1， 0， 1\}$ 或窄高斯分布 $\mathcal{N}(0， \sigma^2)$，其中 $\sigma \ll Q$)中采样，以确保解密时噪声放大系数 $\lVert s(X) \rVert$ 受到严格控制。在 CKKS 的标准实现中，常取 $s(X)$ 的系数从三值分布中采样，即 $\mathbb{P}(s_i = \pm 1) = 1/4$，$\mathbb{P}(s_i = 0) = 1/2$
- 公钥：$\mathsf{pk}=(p_0，p_1)$ 由随机多项式 $a_0(X) \in R_Q$ （掩码）和误差多项式 $e_0(X)$ 构造:
$$
\begin{equation}
p_1 = a_0， \quad p_0 = -a_0 s + e_0 \bmod Q，
\end{equation}
$$
	- 其中 $a_0(X)$ 从均匀分布 $\mathcal{U}(R_Q)$ 中采样，$e_0(X)$ 从离散高斯分布中采样
- 评估密钥（evaluation keys）：用来支持丰富的密文运算
	- 重线性化密钥 $\mathsf{rlk}$: 用于在乘法操作之后将三元密文 $(d_0， d_1， d_2)$ 重新压缩回二元形式，
		- 构造方式为:将 $s^2(X)$ 视为"新的明文"，利用秘密密钥 $s(X)$ 对其进行RLWE加密，生成一组密文对
		- 重线性化过程的本质是一次密钥切换操作，将 $d_2 s^2$ 项通过评估密钥重新表达为 $c'_0 + c'_1 s$ 的形式
	- 旋转与共轭密钥 $\mathsf{rotk}_k$: 用于实现槽位的循环旋转与复共轭操作，是SIMD并行计算的核心支撑。
		- 对于每个需要的旋转步长 $k$ (对应Galois自同构 $\sigma_k: X \mapsto X^k$)，需要预先生成一个旋转密钥 $\mathsf{rotk}_k$
		- 本质是将 $\sigma_k(s(X))$ 视为“新的秘密”，利用原始秘密 $s(X)$ 对其进行加密
	- 模数切换与参数切换密钥: 在部分高级实现中，还会附加用于跨参数域迁移密文的辅助密钥，例如在不同的 $(N， Q)$ 组合之间进行灵活切换
## 1.5. RLWE 加密
模数链（modulus chain）机制:
$$
\begin{equation}
	Q = \prod_{i=0}^{L-1} q_i
\end{equation}
$$
可以近似为：
$$
\begin{equation}
	Q \approx \Delta^L \cdot q_0
\end{equation}
$$
- $q_0$：基础模数（容纳初始噪声与最终解密误差）
- 增大 L 可以提高计算表达能力，需要更大的模数 Q
- 固定环维度 N 的条件下，过大 Q 会削弱 RLWE 问题的困难性
---
使用**剩余数系统（Residue Number System, RNS）** 表示，防止在超大模数 Q 下直接运算
对于多项式环 $R_Q$ 中的任一系数 $p \in \mathbb{Z}_Q$，利用**中国剩余定理(CRT)** 将其分解为 $L$ 个小模数下的剩余表示:
$$
\begin{equation}
	p \quad \Longleftrightarrow \quad (r_0, r_1, \dots, r_{L-1}), \quad \text{其中 } r_i = p \bmod q_i, \quad i=0,\dots,L-1.
\end{equation}

$$
- 多项式加法、乘法可在每个模数通道 $\mathbb{Z}_{q_i}$ 中独立并行执行
- 大模数运算的算术复杂度从 $O(L^2)$ 降至 $O(L)$
- 多项式 $a(X) \in R_Q$ 实际被存储为 $L$ 个"RNS肢"(RNS limbs)组成的多项式向量 $(a^{(0)}(X)， a^{(1)}(X)， \dots， a^{(L-1)}(X))$，其中 $a^{(i)}(X) \in \mathbb{Z}_{q_i}[X]/(X^N+1)$ 为原多项式在模 $q_i$ 下的剩余表示
- 同态加法、乘法与 NTT 变换均在 RNS 通道内独立完成（并行）
- 必要的时候（重缩放、基扩展、密钥切换）需要通多 CRT 重构部分、全部系数
---

- 选定参数 $N， Q， \Delta$ 与误差分布后，**密钥生成(key generation)** 阶段一次性完成秘密密钥 $\mathsf{sk}=(1，s)$ 与公钥 $\mathsf{pk}=(p_0，p_1)$ 的采样，并预计算所需的评估密钥
- 每次乘法(以及某些重线性化、旋转操作) 会放大明文尺度/噪声幅度，所以在其后引入**重缩放(rescaling)** 操作:将整个密文除以当前层的模数 $q_i$ 并丢弃该层，使工作模数从 $Q' = \prod_{j=0}^{i} q_j$ 降至 $Q'' = \prod_{j=0}^{i-1} q_j$，同时将明文尺度从 $\Delta^2$ 重新规范回约 $\Delta$，噪声也相应缩小，从而维持密文的可用性

--- 

给定明文多项式 $m(X) \in R_Q$，公钥加密:
1. 从三值分布中采样短向量 $v(X)$
2. 从离散高斯分布中采样误差多项式 $e_1(X)， e_2(X)$
3. 然后计算：
$$
\begin{equation}
	c_0 = m + p_0 v + e_1 \bmod Q， \quad c_1 = p_1 v + e_2 \bmod Q.
\end{equation}
$$

解密时计算：
$c_0 + c_1 s = m + e_0 v + e_1 + (a_0 v + e_2) s = m + e$，其中 $e = e_0 v + e_1 + e_2 s$ 为总噪声项，包含了加密过程中引入的所有误差成分

## 1.6. 同态运算
设初始加密得到的密文为
$$
\begin{equation}
	\mathbf{c} = (c_0(X)，c_1(X)) \in R_Q^2
\end{equation}
$$
解密时利用秘密密钥 $s(X)$ 计算
$$
\begin{equation}
	m'(X) = c_0(X) + c_1(X)s(X) \bmod Q
\end{equation}
$$
在噪声足够小的前提下，$m'(X)$ 在模 $q_0$ 意义下等价于编码阶段得到的明文多项式 $m(X)$

**同态加法**
两个密文 $\mathbf{c}_1 = (c_{0,1}, c_{1,1})$ 与 $\mathbf{c}_2 = (c_{0,2}, c_{1,2})$ 进行逐分量相加，得到 $\mathbf{c}_\text{add} = (c_{0,1} + c_{0,2}, c_{1,1} + c_{1,2}) \bmod Q$
- 噪声线性叠加
- 明文尺度不变

**同态数乘**
密文 $\mathbf{c}$ 与一个明文常数 $p(X) \in R_Q$ 相乘，得到 $\mathbf{c}_\text{mult} = (p(X) \cdot c_0, p(X) \cdot c_1) \bmod Q$
- 噪声按常数因子线性放大
- 若系数较大，需要额外的噪声管理

**同态乘法**
两个密文 $\mathbf{c}_1 = (c_{0，1}， c_{1，1})$ 与 $\mathbf{c}_2 = (c_{0，2}， c_{1，2})$ 相乘后，会得到一个三元密文 $\mathbf{c}_\text{temp} = (d_0， d_1， d_2)$，其中：
$$
\begin{equation}
	d_0 = c_{0，1} \cdot c_{0，2}， \quad d_1 = c_{0，1} \cdot c_{1，2} + c_{1，1} \cdot c_{0，2}， \quad d_2 = c_{1，1} \cdot c_{1，2}.
\end{equation}
$$
解密该三元密文可得 $d_0 + d_1 s + d_2 s^2 \approx m_1(X) \cdot m_2(X) \cdot \Delta + e_\text{mult}$，其中 $e_\text{mult}$ 为乘法引入的噪声项。此时明文尺度已从 $\Delta$ 增长至约 $\Delta^2$，而密文维度也从二元膨胀至三元，这两个变化都需要通过后续操作进行修正：
1. **重线性化(relinearization)** : 利用预先生成的**重线性化密钥(relinearization key)** $\mathsf{rlk}$，将三元密文 $\mathbf{c}_\text{temp}$ 重新映射回二元形式 $\mathbf{c}_\text{rl} = (c'_0， c'_1) \in R_Q^2$，同时引入约 $O(1/Q \cdot \lVert s^2 \rVert)$ 量级的额外噪声
2. 重缩放：
$$
\begin{equation}
	\mathbf{c}'= \left( \left\lfloor \frac{c_0}{q_i} \right\rceil， \left\lfloor \frac{c_1}{q_i} \right\rceil \right) \in R_{Q/q_i}^2，
\end{equation}
$$

**槽位旋转与共轭**
- 循环旋转 (rotation)：做移位，依赖于**Galois 自同构 (Galois automorphism)**[^2]，利用预先生成的旋转密钥 $\mathsf{rotk}_k$，可以将密文 $\mathbf{c}$ （使用新密钥的密文）变换为 $\mathbf{c}'$（原来密钥对应的密文）
- 复共轭 (conjugation)

## 1.7. decryption
云端完成计算后，将结果密文 $\mathbf{c}_\mathrm{out} = (c_{0，\mathrm{out}}， c_{1，\mathrm{out}}) \in R_{Q'}^2$ 返回给客户端，此时工作模数可能已降至 $Q' < Q$。客户端使用秘密密钥 $s(X)$ 执行**解密**操作:
$$
\begin{equation}
	m_\mathrm{out}(X) = c_{0，\mathrm{out}}(X) + c_{1，\mathrm{out}}(X)s(X) \bmod Q'.
\end{equation}
$$
如果噪声始终显著小于当前工作模数(即满足$\lVert e(X) \rVert_\infty \ll Q'$)，则$m_\mathrm{out}(X)$与期望的"理想明文多项式"$m_\text{ideal}(X)$仅相差一个小的噪声多项式$e(X)$，即
$$
\begin{equation}
	m_\mathrm{out}(X) = m_\text{ideal}(X) + e(X).
\end{equation}
$$
随后客户端进行解码：

## 1.8. decoding
步骤：
1. 尺度归一化：将$m_\mathrm{out}(X)$的整数系数除以当前尺度(通常仍约为$\Delta$)，得到近似实系数多项式$\tilde{P}(X) = m_\mathrm{out}(X) / \Delta$，恢复了原始数值的浮点表示
2. 正向傅里叶变换：计算 $\tilde{P}(X)$ 在 $2N$ 次本原单位根的奇次幂 $\xi， \xi^3， \dots， \xi^{2N-1}$ 处的取值，得到长度为 $N/2$ 的复向量: 
$$
\begin{equation}
  \tilde{\mathbf{z}}
\mathrm{out} = (\tilde{P}(\xi)， \tilde{P}(\xi^3)， \dots， \tilde{P}(\xi^{2N-1}))
\end{equation}
$$
3. 提取有效槽位：从 $\tilde{\mathbf{z}}_\mathrm{out}$ 中提取前 $n$ 个槽位，得到应用层接收的近似明文结果 $\mathbf{z}_\mathrm{out} = (z_{0，\mathrm{out}}， \dots， z_{n-1，\mathrm{out}}) \in \mathbb{C}^n$，完成从密文到明文的完整转换过程 

# 2. 数据结构
## 2.1. 密文数据结构
密文数据经过RNS分解之后，每个密文主要由两组多项式组成，即 $\mathbf{ct} = (c_0， c_1)$
- 对于多项式环 $R_q = \mathbb{Z}_q[X]/(X^N+1)$，
- 每个多项式 $c_i$ 包含 $N$ 个系数
- 在RNS表示下，模数 $q$ 被分解为 $L$ 个互质的小模数 $q_0， q_1， \ldots， q_{L-1}$ 的乘积
单个密文分量 $c_i$ 的数据结构可表示为一个 $L \times N$ 的矩阵:
$$
\begin{equation}
c_i = \begin{bmatrix}
c_i^{(0)}[0] & c_i^{(0)}[1] & \cdots & c_i^{(0)}[N-1] \\
c_i^{(1)}[0] & c_i^{(1)}[1] & \cdots & c_i^{(1)}[N-1] \\
\vdots & \vdots & \ddots & \vdots \\
c_i^{(L-1)}[0] & c_i^{(L-1)}[1] & \cdots & c_i^{(L-1)}[N-1]
\end{bmatrix}
\end{equation}
$$
-  $c_i^{(j)}[k]$ 表示多项式 $c_i$ 的第 $k$ 个系数在模 $q_j$ 下的取值
- 完整的密文 $\mathbf{ct}$ 则由两个这样的 $L \times N$ 矩阵构成，在存储系统中占用 $2 \times L \times N$ 个模运算单元的存储空间

## 2.2. 明文数据结构

原始明文向量 $\mathbf{m} = (m_0， m_1， \ldots， m_{N/2-1})$ 包含最多 $N/2$ 个复数或实数元素。经过编码变换（通常为离散傅里叶变换的变种）后，明文被转换为多项式 $m(X) \in R$，该多项式同样具有 $N$ 个系数
- 初始阶段，仅在基础模数 $q_0$ 下表示：
$$
\begin{equation}
m_{\text{init}} = \begin{bmatrix}
m^{(0)}[0] & m^{(0)}[1] & \cdots & m^{(0)}[N-1]
\end{bmatrix}
\end{equation}
$$
	- $m^{(0)}[k]$ 表示明文多项式的第 $k$ 个系数在模 $q_0$ 下的取值
- 明文需要与具有更高模数层级的密文进行同态运算时：必须将明文的模数表示从单一的 $q_0$ 扩展到与密文相匹配的完整RNS模数集合 $\{q_0， q_1， \ldots， q_{L-1}\}$
	- 对于明文多项式的每个系数$m[k] \bmod q_0$，通过CRT插值计算得到其在$q_1， q_2， \ldots， q_{L-1}$各模数下的表示值$m^{(1)}[k]， m^{(2)}[k]， \ldots， m^{(L-1)}[k]$，从而将明文数据结构扩展为完整的$L \times N$矩阵形态:
$$
\begin{equation}
m_{\text{expanded}} = \begin{bmatrix}
m^{(0)}[0] & m^{(0)}[1] & \cdots & m^{(0)}[N-1] \\
m^{(1)}[0] & m^{(1)}[1] & \cdots & m^{(1)}[N-1] \\
\vdots & \vdots & \ddots & \vdots \\
m^{(L-1)}[0] & m^{(L-1)}[1] & \cdots & m^{(L-1)}[N-1]
\end{bmatrix}
\end{equation}
$$
	-  $m^{(j)}[k]$ 表示明文多项式的第 $k$ 个系数在模 $q_j$ 下的表示
## 2.3. 密钥数据结构
- 私钥 $\mathbf{sk}$ 通常为单个多项式 $s(X) \in R$，其系数服从特定的概率分布（如三元分布或高斯分布）。在RNS表示下，私钥的数据结构为 $L \times N$ 矩阵：
$$
\begin{equation}
s = \begin{bmatrix}
s^{(0)}[0] & s^{(0)}[1] & \cdots & s^{(0)}[N-1] \\
s^{(1)}[0] & s^{(1)}[1] & \cdots & s^{(1)}[N-1] \\
\vdots & \vdots & \ddots & \vdots \\
s^{(L-1)}[0] & s^{(L-1)}[1] & \cdots & s^{(L-1)}[N-1]
\end{bmatrix}
\end{equation}
$$
- 公钥 $\mathbf{pk}$ 由一对多项式 $(pk_0， pk_1)$ 组成，其数据结构与密文结构完全相同，包含两个 $L \times N$ 矩阵 
- 评估密钥：用来实现密钥切换（key switching）
	- 特征在于内部数字分解结构：目标多项式（如 $s^2$ 或旋转后的 $s$）需要被分解为 $d$ 个较小的部分，每个部分对应一组独立的密钥对 
	- 这 $d$ 组密钥对在数据分布形式上呈现差异：它们分别对应目标多项式在不同数字基（digit base）下的分解分量 
	- 以重线性化密钥为例，其完整数据结构可表示为 $d$ 组密钥对的集合： 
$$
\begin{equation}
\mathbf{rlk} = \{(rlk_{0，0}， rlk_{0，1})， (rlk_{1，0}， rlk_{1，1})， \ldots， (rlk_{d-1，0}， rlk_{d-1，1})\}
\end{equation}
$$
		- 其中每组 $(rlk_{i，0}， rlk_{i，1})$ 对应目标多项式 $s^2$ 的第 $i$ 层数字分解，两者均为 $L \times N$ 矩阵，但其系数分布反映了不同的分解层级信息
		- 整个重线性化密钥在存储空间中占用 $d \times 2 \times L \times N$ 个元素
- 旋转密钥：数据结构与重线性化密钥在形式上保持一致。对于支持 $r$ 种旋转步长的系统，每种旋转步长需要存储一组完整的密钥切换密钥，即 $d$ 个密钥对。总存储开销为 $r \times d \times 2 \times L \times N$ 个元素 

# 3. 计算流程
## 3.1. 密钥切换计算流程
同态乘法、旋转操作使原始密钥无法解密，需要在操作后进行 KeySwitch（密钥切换），将密文从中间密钥空间转换回原始密钥空间：
$$
\text{KeySwitch}(\text{ct}_{s'}) \rightarrow \text{ct}_{s}
$$
-  $\text{ct}_{s'}$ 表示处于“中间”密钥 $s'$（例如 $s^2$ 或经过旋转变换的密钥）下的密文
-  $\text{ct}_s$ 则是转换后处于原始解密密钥 $s$ 下的密文 

三个阶段：
1. ModUp(模数提升):数据扩展阶段
2. KeyMult(密钥乘法)
3. ModDown(模数降低)

## 3.2. 自举操作计算流程
自举操作 (Bootstrap) ：全同态加密中刷新密文、降低噪声的关键技术
1. 模数提升 (ModRaise)
2. 系数到槽转换 (Coefficientto-Slot,C2S)：密文从标准的多项式系数表示转换为槽编码 (Slot Encoding)，等价于离散傅里叶变换 (DFT)，降低计算复杂度
3. 模数求值  (EvalMod)
4. 槽到系数转换 (Slot-to-Coefficient,S2C)：等价于逆离散傅里叶变换 (Inverse DFT,IDFT)

[^1]: Number Theoretic Transform (NTT，数论变换)：纯整数运算将多项式乘法复杂度降到 $O(N\log N)$
	- FFT 快速傅里叶变换的变体：FFT 需要处理浮点数，计算机处理浮点数有精度问题
	- N 需要是 2 的次幂：NTT 采用的分治的思想，可以二分到底

[^2]: 对于多项式 $a(X) \in R_Q$，Galois 自同构定义为 $\sigma_k: X \mapsto X^k$，其中 $\gcd(k， 2N) = 1$
