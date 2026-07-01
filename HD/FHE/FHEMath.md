# 1. Modulo
- a mod q: a 除 q 的余数
- q: 模数（modulus）
- $a\equiv b\operatorname{mod}q$：a 与 b 模 q 的值相同，等同于 $a = b(\operatorname{mod}q)$
- $a\equiv b\operatorname{mod}q\Longleftrightarrow a=b+k\cdot q$

## 1.1. Modulo Arithmetic
- Addtion: $a\equiv b\mathrm{~mod~}q\Longleftrightarrow a+x\equiv b+x\mathrm{~mod~}q$
- Subtraction: $a\equiv b\mathrm{~mod~}q\Longleftrightarrow a-x\equiv b-x\mathrm{~mod~}q$
- Multiplication: $a\equiv b\mathrm{~mod~}q\Longleftrightarrow a\cdot x\equiv b \cdot  x\mathrm{~mod~}q$，只有当 $\text{gcd}(x,q)=1$ 的时候成立，否则 $a\equiv b\mathrm{~mod~}q\Rightarrow a\cdot x\equiv b\cdot x\mathrm{~mod~}q$
- Associative: $(a\cdot b)\cdot c\equiv a\cdot(b\cdot c)\mathrm{~mod~}q$
- Commutative: $(a\cdot b)\equiv(b\cdot a){\mathrm{~mod~}}q$
- Distributive: $(a\cdot(b+c))\equiv((a\cdot b)+(a\cdot c)){\mathrm{~mod~}}q$
- Interchangeable: 若 $a\equiv b\mathrm{~mod~}q$，且 $a\equiv b\mathrm{~mod~}q$，则：
$$\begin{aligned}
 & (a+c)\equiv(b+d)\equiv(a+d)\equiv(b+c){\mathrm{~mod~}}q \\
 & (a-c)\equiv(b-d)\equiv(a-d)\equiv(b-c){\mathrm{~mod~}}q \\
 & (a\cdot c)\equiv(b\cdot d)\equiv(a\cdot d)\equiv(b\cdot c){\mathrm{~mod~}}q
\end{aligned}$$
## 1.2. Inverse
在模 q 中数字只有 $\{0,1,2,\dots,q-1\}$
- Additive Inverse: $a^{-1}_{+}$ 满足 $a+a^{-1}_{+}\equiv 0 \mathrm{~mod~}q$
- Multiplicative Inverse: $a^{-1}_{*}$ 满足 $a\cdot a^{-1}_{*}\equiv 1 \mathrm{~mod~}q$

## 1.3. Centered Residue Representation
- Canonical residue representation: $\{0,1,2,\dots,q-1\}$
	- 若 $a+b\mathrm{~mod~}q$ 在 $[0,q-1]$ 之间，可以简化成 $a+b$
- Centered residue repersentation: $\left\{-\frac{q}{2},-\frac{q}{2}+1,\cdots,0,\cdots,\frac{q}{2}-2,\frac{q}{2}-1\right\}$
	- 若 $a-b\mathrm{~mod~}q$ 在 $[-\frac{q}{2},\frac{q}{2}-1]$ 之间，可以简化成 $a-b$

# 2. Goup
## 2.1. Elements
- Set: $\mathbb{S} = \{a, b, c, \ldots\}$
- Operations: addition(+) 和 multiplicaton($\cdot$)
- Additive Identity ($0_{(+)}$ 常记作 0)：元素 $i \in \mathbb{S}$，使得对任意 $a \in \mathbb{S}$ 都有 $i + a = a = a + i$，则 $i$ 称为加法单位元
- Multiplicative Identity ($1_{(\cdot)}$ 常记作 1): 元素 $i \in \mathbb{S}$，使得对任意 $a \in \mathbb{S}$ 都有 $i \cdot a = a = a \cdot i$，则 $i$ 称为乘法单位元
- Additive Inverse ($a^{-1}_{(+)}$，常记作 -a): 每个 $a \in \mathbb{S}$，若存在元素 $a^{-1}_{(+)} \in \mathbb{S}$，使得 $a + a^{-1}_{(+)} = 0_{(+)} = a^{-1}_{(+)} + a$，则 $a^{-1}_{(+)}$ 称为 $a$ 的加法逆元
- Multiplicative Inverse ($a \in \mathbb{S}$, 常记作 $a^{-1}$)：关于 $\cdot$ 可逆的元素 $a \in \mathbb{S}$，若存在元素 $a^{-1}_{(\cdot)} \in \mathbb{S}$，使得 $a \cdot a^{-1}_{(\cdot)} = 1_{(\cdot)} = a^{-1}_{(\cdot)} \cdot a$，则 $a^{-1}_{(\cdot)}$ 称为 $a$ 的乘法逆元

## 2.2. Operation Features
- Closed: 两个元素运算后仍然在集合内
	- 集合 $\mathbb{S}$ 对运算 $+$ 封闭，指的是：$\forall a,b \in \mathbb{S}, \quad a+b \in \mathbb{S}$
	- 集合 $\mathbb{S}$ 对运算 $\cdot$ 封闭，指的是：$\forall a,b \in \mathbb{S}, \quad a \cdot b \in \mathbb{S}$
- Associative: 对任意 $a,b,c \in \mathbb{S}$，如果：$(a+b)+c = a+(b+c)$，则运算 $+$ 满足结合律
- Commutative: 对任意 $a,b \in \mathbb{S}$，如果：$a+b = b+a$，则运算 $+$ 满足交换律
- Distributive: 如果集合上同时定义了 $+$ 与 $\cdot$，例如在环结构中，则可以讨论分配律：$a \cdot (b+c) = (a \cdot b) + (a \cdot c)$ 和 $(a+b)\cdot c = a \cdot c + b \cdot c$

## 2.3. Group Types
- Semigroup: set + 一个运算，并满足封闭性、结合律，可记作 $(\mathbb{S}, +)$ 或 $(\mathbb{S}, \cdot)$
- Monoid: semigroup + 单位元
- Group: monoid + 每个元素都有逆元
- Abelian Group: group + 交换律

# 3. Field
- Ring: 集合 R 上定义加法、乘法，是 Abelian group，但是乘法不需要满足交换律（如矩阵乘法）
- Field: 集合 $F$ 加法下的 Abelian group，除了 0 元素（$F^\times = F \setminus \{0\}$）可以在乘法下构成 Abelian group，满足乘法分配律
- Galois Field（$\mathrm{GF}(p^n)$）: 大小为 $p^n$ 的有限 field（$p$ 是素数，n 是正整数）
- $\mathbb{Z}_p$（$\mathbb{Z}/p\mathbb{Z}$）：一个素数 p，集合 $\{0,1,2,\dots,p-1\}$ 和加法、乘法形成 field；广泛的，对任何整数 $m\geq 2$，$\mathbb{Z}_m$ 是个满足交换律的 ring

## 3.1. Theorems
- Size of Finite Field: 一定是素数的幂次，翻过来每一个速度幂次都存在域
- Isomorphic Fields: 任何两个有限域 $\mathbb{F}_1$ 、$\mathbb{F}_2$ 是同构的，存在双射：$f: \mathbb{F}_{1} \rightarrow \mathbb{F}_{2}$，对于所有 $a, b \in \mathbb{F}_{1}$，有 $f(a+b)=f(a)+f(b)、f(a b)=f(a) f(b)$

# 4. Order
## 4.1. Definitions
- $\mathrm{ord}_{\mathbb{F}}(\mathbf{a})$:a 属于有限域，$\mathrm{ord}(a)=\min\{k>0:a^k=1\}$

## 4.2. Theorems
- 对 $a\in\mathbb{F}^{\times}$ 且 $n\geq1\mathrm{,~}a^{n}=1$，当仅当 $\mathrm{ord}_{\mathbb{F}}(a)\mid n$ 成立
- 如果 $\mathrm{ord}_{\mathbb{F}}(a)=k$，对于任何 $n\geq1$，则 $\mathrm{ord}_{\mathbb{F}}(a^n)=\frac{k}{\gcd(k,n)}$
- 如果 k 能除 n，$\mathrm{ord}_{\mathbb{F}}(a)=kn$，当仅当 $\mathrm{ord}_{\mathbb{F}}(a^k)=n$ 时成立
- $\left|\mathbb{F}\right|=p$ (p 是素数)，$a\in\mathbb{F}$，则 $a^p=a$

# 5. Polynomial Ring
多项式环：在多项式之上定义的加减运算
对于多项式环 $f\in\mathbb{Z}_p[x]/(x^n+1)$ 可简写成 $\mathscr{R}_{\langle n,p\rangle}$，其 $f=c_0+c_1x^1+\cdots+c_{n-1}x^{n-1}$ 有以下性质：
- Coefficient Ring: 每个系数 $c_{j}\in\mathbb{Z}_{p}$
- Degree Bound: 所有 $f^{\prime}\in\mathbb{Z}_p[x]$，可以被写作：$f^{\prime}=(x^n+1)f_q+f_r,\quad\deg f_r<n$
	- 在一个商环 $f\in\mathbb{Z}_p[x]/(x^n+1)$ 内，$f^{\prime}\equiv f_{r}(modx^{n}+1)$
	- $f_q$: 商多项式
	- $f_r$: 余多项式
- Polynomial Congruence：如果两个多项式同余，那么他们是等价类，可以在多项式环 $(+,\cdot)$ 的运算上互换，例如：
	- 若：
$$\begin{aligned}
 & f^{\prime}\equiv f_{r1}\in\mathbb{Z}_{p}[x]/(x^{n}+1) \\
 & f^{\prime\prime}\equiv f_{r2}\in\mathbb{Z}_{p}[x]/(x^{n}+1) \\
 & f_{r1}+f_{r2}\equiv f_{r3}\in\mathbb{Z}_{p}[x]/(x^{n}+1)
\end{aligned}$$
	- 则 $f^{\prime}+f^{\prime\prime}\equiv f_{r1}+f_{r2}\equiv f_{r3}\in\mathbb{Z}_{p}[x]/(x^{n}+1)$
- **Polynomial Evaluation**: 如果 $M_1$ 、$M_2$ 是同余多项式，且 $X=x_i$ 是 $x^n+1$ 的解，$M_1(x_i)=M_2(x_i)$

## 5.1. Coefficient Rotation
系数旋转：移动整个多项式的系数，如果向左移动 $h$，则乘 $x^{-h}$

# 6. Decomposition
## 6.1. Number Decompositon
固定一个模 $q\geq2$，$\mathbb{Z}_q=\mathbb{Z}/q\mathbb{Z}$，让 $\gamma\in\mathbb{Z}_{q}$，在基 $\beta$ 下数字分解：
$$\begin{aligned}
\gamma=\gamma_1\frac{q}{\beta^1}+\gamma_2\frac{q}{\beta^2}+\cdots+\gamma_\ell\frac{q}{\beta^\ell}
\end{aligned}$$
- $\ell$：分解层数
- $\beta^{\ell}\mid q$：可以分解干净且唯一

将 $\gamma$ 分解成以 $\beta$ 为基的 $\ell$ 层记作：
$$\mathrm{Decomp}^{\beta,\ell}(\gamma)=(\gamma_1,\gamma_2,\cdots,\gamma_\ell).$$

## 6.2. Polynomial Decomposition
给定 $f\in\mathbb{Z}_q[x]/(x^n+1)$，固定 $\beta\geq2$ 和 $\ell \geq 1$，并且 $\beta^{\ell}\mid q$，可以分解为：
$$f=\sum_{i=1}^{\ell}f_{i}\frac{q}{\beta^{i}},\quad f_{i}\in\mathbb{Z}_{q}[x]/(x^{n}+1)$$
记作：
$$\mathrm{Decomp}^{\beta,\ell}(f)=(f_1,f_2,\cdots,f_\ell)$$
## 6.3. Approximate Decomposition
如果没有 $\ell$ 可以满足 $\beta^{\ell}\mid q$，需要将一些低位舍入到 $\frac{q}{\beta^\ell}$，写成：
$$\gamma=\sum_{i=1}^\ell\gamma_i\frac{q}{\beta^i}+\varepsilon,\quad\gamma_i\in\{0,\ldots,\beta-1\},\quad|\varepsilon|\leq\frac{q}{2\beta^\ell}$$
## 6.4. Gadget Decomposition
按如下方式分解 $\gamma$：
$$
\gamma=\gamma_{1} g_{1}+\gamma_{2} g_{2}+\cdots+\gamma_{\ell} g_{\ell}
$$
- gadget 向量：$\vec{g}=\left(g_{1}, g_{2}, \cdots, g_{\ell}\right)$
- $\operatorname{Decomp}^{\vec{g}}(\gamma)=\left(\gamma_{1}, \gamma_{2}, \cdots, \gamma_{\ell}\right)$
- $\gamma=\left\langle\operatorname{Decomp}^{\vec{g}}(\gamma), \vec{g}\right\rangle$

# 7. Roots of Unity
- n-th root of Unity: 复数 $\zeta$，满足 $\zeta^{n}=1$
- Primitive n-th Root of Unity ($P(n)$): n-th root of unity $\zeta$，并且满足 $\mathsf{ord}_\mathbb{C}(\zeta)=n$

## 7.1. Theorems
- 给定 $\zeta^{n}=1$，有 n 个不同的 n-th roots of unity:
$$\zeta=e^{2k\pi i/n}=\cos\left(\frac{2k\pi}{n}\right)+i\cdot\sin\left(\frac{2k\pi}{n}\right)$$
- 给定 $\zeta\in\mathbb{C}$、$\zeta^{n}=1$，$\zeta$ 是 n-th root of unity，当且仅当 $\mathsf{ord}_{\mathbb{C}}(\zeta)\mid n$
- n-th roots of unity 的集合：$\bigcup_{d\mid n}P(d)$
- 给定 n-th roots of unity $\zeta=\omega^k$（$k=\{0,1,\cdots,n-1\}$，$\omega=e^{2\pi i/n}$），$\zeta$ 是 primitive n-th root of unity 当且仅当 $\mathrm{gcd}(n,k)=1$
- primitive n-th roots of unity 的数量（正好是欧拉函数）：
$$\varphi(n)=|\{\left.k:1\leq k\leq n,\mathrm{~gcd}(k,n)=1\right.\}|$$

# 8. Lagrange's Polynomial Interpolation
给定 n+1 个二维坐标 $(x_0,y_0),(x_1,y_1),\cdots,(x_n,y_n)$，所有 $X$ 不等，$Y$ 不需要不等，$(X,Y)$ 可以是 $(x_i,y_i)\in\mathbb{C}^2$ 或 $(x_i,y_i)\in\mathbb{Z}_p^2$（p 为素数），存在唯一的多项式 $f(X)$ 度数至多为 $n$，可以穿过 $n+1$ 个坐标， $f(X)$ 可以计算：
$$\begin{aligned}
f(X) & =\sum_{j=0}^n\frac{(X-x_0)\cdot(X-x_1)\cdots(X-x_{j-1})\cdot(X-x_{j+1})\cdots(X-x_n)}{(x_j-x_0)\cdot(x_j-x_1)\cdots(x_j-x_{j-1})\cdot(x_j-x_{j+1})\cdots(x_j-x_n)}\cdot y_j \\
 & =\sum_{j=0}^n\left(\prod_{0\leq k\leq n}\frac{X-x_k}{x_j-x_k}\cdot y_j\right)
\end{aligned}$$
## 8.1. The Field Condition for Unique Interpolation
如果多项式定义在域 $\mathbb{F}$ 上（$x$、$y$ 坐标和其系数在域 $\mathbb{F}$ 上），对于 $\mathbb{F}$ 上不等的 n+1 个 x 和任意 n+1 个 y，存在唯一的且最高为 n 次的多项式来插值

# 9. Cyclotomic Polynomial
- n-th Cyclotomic Polynomial（分圆多项式）: 以 primitive n-th roots of unity 为根的多项式
$$\Phi_n(x)=\prod_{\zeta\in P(n)}(x-\zeta)=\prod_{\overset{0\leq k\leq n-1,}{\operatorname*{\operatorname*{gcd}(k,n)=1}}}(x-\omega^k)\quad\text{, where }\omega=e^{2\pi i/n}$$
- 一些预计算的分圆多项式：
$$\begin{aligned}
 & \Phi_{1}(x)=x-1 \\
 & \Phi_{2}(x)=x+1 \\
 & \Phi_{3}(x)=x^2+x+1 \\
 & \Phi_{4}(x)=x^{2}+1 \\
 & \Phi_{5}(x)=x^4+x^3+x^2+x+1 \\
 & \Phi_6(x)=x^2-x+1 \\
 & \Phi_7(x)=x^6+x^5+x^4+x^3+x^2+x+1 \\
 & \Phi_8(x)=x^4+1 \\
 & \Phi_9(x)=x^6+x^3+1 \\
 & \Phi_{10}(x)=x^4-x^3+x^2-x+1
\end{aligned}$$
## 9.1. Theorems
- 若 $M$ 是 2 的次幂，且 $M$ -th 分圆多项式 $\Phi_M(x)=x^n+1$（$M=2n$），根为：$\omega,\omega^{3},\omega^{5},\cdots,\omega^{2n-1}$（$\omega=e^{2\pi i/n}$）
- 对于任何正整数 $n$:
$$x^n-1=\prod_{d|n}\Phi_d(x)$$
- 一个分圆多项式只有整系数
- 如果 $k\mid n$，则 $\Phi_{nk}(x)=\Phi_n(x^k)$


# 10. Vector and Matrix
## 10.1. Vector Arithmetic
- Hadamard Product: 给定 $\overrightarrow{a}=(a_0,a_1,\ldots,a_{n-1}),\overrightarrow{b}=(b_0,b_1,\ldots,b_{n-1})$，结果：
$$\overrightarrow{a}\odot\overrightarrow{b}=(a_0b_0,a_1b_1,\cdots,a_{n-1}b_{n-1})$$
- Hermitian Product：给定两个复数向量 $\overrightarrow{a}=(a_0+i\cdot a_0^\prime,a_1+i\cdot a_1^\prime,\ldots,a_{n-1}+i\cdot a_{n-1}^\prime),\overrightarrow{b}=(b_0+i\cdot b_0^\prime,b_1+i\cdot b_1^\prime,\ldots,b_{n-1}+i\cdot b_{n-1}^\prime)$，结果：
$$\begin{aligned}
 & \langle\langle\overrightarrow{a},\overrightarrow{b}\rangle\rangle=\overrightarrow{a}\cdot\overline{\overrightarrow{b}} \\
 & =(a_0+i\cdot a_0^{\prime},a_1+i\cdot a_1^{\prime},\cdots,a_{n-1}+i\cdot a_{n-1}^{\prime})\cdot(b_0-i\cdot b_0^{\prime},b_1-i\cdot b_1^{\prime},\cdots,b_{n-1}-i\cdot b_{n-1}^{\prime})
\end{aligned}$$
## 10.2. Matrix Types
- Vandermonde matrix:
$$Vander(x_0,x_1,\cdots,x_m)=
\begin{bmatrix}
1 & x_0 & x_0^2 & \cdots & x_0^n \\
1 & x_1 & x_1^2 & \cdots & x_1^n \\
1 & x_2 & x_2^2 & \cdots & x_2^n \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
1 & x_m & x_m^2 & \cdots & x_m^n
\end{bmatrix}$$
## 10.3. Matrix Arithmetic
- Associative
$$\begin{aligned}
 & (AB)C=A(BC) \\
 & A(Bx)=(AB)x
\end{aligned}$$
- Distributive
$$A(x+y)=Ax+Ay$$
- NOT Commutative
$$\begin{aligned} Ax\neq xA \\
AB\neq BA \end{aligned}$$
## 10.4. Projection
- Orthogonal Basis: 若 $\overrightarrow{p_0},\overrightarrow{p_1},\cdots,\overrightarrow{p_{n-1}}$ 两两正交，则 $P= \begin{bmatrix} \overrightarrow{p_0} \\ \overrightarrow{p_1} \\ \vdots \\ \overrightarrow{p_{n-1}} \end{bmatrix}$ 为一个正交基
- Orthogonal Projection: 对于一个正交基 $P= \begin{bmatrix} \overrightarrow{p_0} \\ \overrightarrow{p_1} \\ \vdots \\ \overrightarrow{p_{n-1}} \end{bmatrix}$，向量 $\overrightarrow{a}$ 在 $P$ 上的投影：$\mathsf{Proj}_P(\overrightarrow{a})=\sum_{i=0}^{n-1}\mathsf{Proj}_{\overrightarrow{p_i}}(\overrightarrow{a})$
- 对于正交基，可以有唯一投影
- 对于非正交基，可以有唯一投影，但是不能用原来方式计算（$P\overrightarrow{v}$）

## 10.5. Basis of a Polynomial Ring
对于 n - 1 度的多项式环 $\mathbb{Z}[X]/(X^n+1)$，基满足：
- Linear Independence: 每个多项式不能被表示成其他多项式的线性组合
- Spanning the Polynomial Ring: 基中的多项式可以线性组合表达所有多项式环中的多项式
- 基中多项式数量为：n







