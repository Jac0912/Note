# 算法 

## 0.输入输出

**0.1多行输入，每行两个整数**
```python
import sys
for line in sys.stdin:
    a, b = line.split(' ')
    print(int(a) + int(b))
    print()
```

**0.2多组数据，每组第一行为n，之后输入n行两个整数**
```python
while 1:
    try:
        N = int(input())
        for i in range(N):
            l = list(map(int, input().split()))
            print(sum(l))
    except:
        break
#input():从标准输入读入一行，以字符串形式返回
#map(function, iterable):将iterable用function进行映射。惰性求值，只有在需要值时才会计算。返回map对象，需用list()进行转换
```

**0.3若干行输入，每行输入两个整数，遇到特定条件终止**
```python
import sys
while True:
    s = input().split()
    a, b= int(s[0]), int(s[1])
    if not a or not b:
        break
    print(a + b)
```

**0.4若干行输入，遇到0终止，每行第一个数为N，表示本行后面N个数**
```python
import sys
for line in sys.stdin:
    nums = line.split()
    nums = list(map(int, nums))
    n = nums[0]
    if not n:
        break
    print(sum(nums[-n:]))
```

**0.5若干行输入，每行包括两个整数a、b，由空格分隔，每行输出后接一个空行**
```python
while True:
    try:
        x, y = map(int, (input().split()))
        print(x + y)
        print()
    except:
        break
```

**0.6多组n行数据，每行先输入一个整数N，然后在同一行内输入M个整数，每组输出之间输出一个空行**
```python
while 1:
    try:
        N = int(input())
        for i in range(N):
            n = list(map(int, input().split()))
            if n[0] == 0:
                print()
                continue
            print(sum(n[1:]))
            if i < N - 1:
                print()
    except:
        break
```

**0.7多组测试样例，每组输入数据为字符串，字符用空格分隔，输出为小数点后两位**
```python
#for-else：可以在for循环正常结束（未break）后执行else语句中的内容
while True:
    try:
        n = input().replace(' ', '').replace('A', '4').replace('B', '3').replace('C', '2').replace('D', '1').replace('F', '0')
        s = 0
        for i in n:
            if i not in '43210':
                print('Unknown')
                break
            s += int(i)
        else:
            print(f'{s / len(n):.2f}')
    except:
        break
```

**0.8多组测试数据，首先输入一个整数N，接下来N行每行输入两个整数a和b，读取输入数据到Map**
```python
while True:
    try:
        N = int(input())
        myMap = {}
        for _ in range(N):
            a, b = map(int, input().split())
            myMap[a] = b
    except:
        break
```

## 1.基础算法
### 1.1排序
```python
sorted(<list>)  #返回以排序的新列表，不修改原列表
sorted(<list>, reverse=True)  #降序
sorted(__iterable, key=lambda x: x[1])  #按第二个元素排序(自定义排序)

<list>.sort()  #将原列表就地排序，返回值为null
<list>.sort(reverse=True)  #降序
<list>.sort(key=lambda x: x[1])  #按第二个元素排序(自定义排序)
```

### 1.2二分
**整数二分**
```python
bisect.insort(<list>, num, lo=0, hi=len(<list>))  #将num插入<list>，并维持排序(假设已经排序，若存在则插入靠右的地方)，可通过lo、hi指定插入区间
bisect.bisect(<list>, num, lo=0, hi=len(<list>))  #返回应该插入num的下标，其它同上

bisect.insort_right(<list>, num, lo=0, hi=len(<list>))  #将num插入靠右的地方
bisect.bisect_right(<list>, num, lo=0, hi=len(<list>))  #返回应该插入num的下标，其它同上

bisect.insort_left(<list>, num, lo=0, hi=len(<list>))  #将num插入靠左的地方
bisect.bisect_left(<list>, num, lo=0, hi=len(<list>))  #返回应该插入num的下标，其它同上
```

**King**
![image-20240229165813532](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240229165813532.png)

```python
left = -1
right = N
while left + 1 != right :
	mid = (left + right) // 2
	if isBlue(mid) :
		left = mid
	else :
		right = mid;
return right  #或left(依情况定)
```

1. **建模：划分红蓝区域**
2. 找蓝色区域数据的性质
3. 确定返回值的位置（left or right）
4. 判断没找到的情况

**浮点二分**
精度设置：比要求保留的位数小$10^{-2}$倍
```python
double right = 1e5;
double left = 0;
while (right - left > 0.01) {
	double mid = (left + right) / 2;
	if (check(mid, L, K, N)) {
		left = mid;
	} else {
		right = mid;
	}
}
```

### 1.3前缀和
```java
int []a=new int[10];
int []sum=new int[11];
for(int i=1;i<11;i++)
{
    sum[i]=a[i-1]+sum[i-1];
}
```
\[i, j]所有元素的和：sum\[j]-sum\[i-1]

### 1.4差分
**改变数组多个区间**  $O(1)$
exp：区间L,R加a
- 差分数组：save[0]=arr[0] (定基数)    save[i]=arr[i]-arr[i-1] 
- save[L]+=a  save[R+1]-=a(消除对区间外的影响)
- 对save数组求前缀和得结果

### 1.5双指针
将$O(n^2)$的遍历复杂度降到$O(n)$
```python
j = 0
for i in range(n):
	while j < i and check(i, j) :
		j += 1
	#题目具体逻辑
```

### 1.6位运算
n的二进制表示中第k位是几：
n >> k & 1
```python
n = 10  
for i in range(3, -1, -1):  
    print(n >> i & 1, end=" ")
```

lowbit(x)：返回 x 最后一位1
x & -x = x & (~x + 1)
应用：统计二进制x里1的个数

### 1.7离散化
alls\[ ]数组(存储所有用到的下标)值域较大，将每个值映射到下标\[0, n]
1. 对alls\[ ]数组去重、排序
2. 二分求出x(待查询的大下标)对应离散化的下标

### 1.8区间合并
1. 按区间左端点排序
2. 分三种情况合并区间

## 2.数据结构
### 2.1并查集
1. 将两个集合合并
2. 询问两个元素是否在一个集合当中
每个集合用树来表示，树根的编号是整个集合的编号，每个节点存储父节点，p\[x]表示x的父节点
- 判断树根：p\[x] == x
- 求x的集合编号：
	while p\[x] != x :
		x = p\[x]
	- 合并集合：若p\[x]为x的集合编号，p\[y]为y的集合编号。p\[x] = y

优化（路径压缩）：寻找x所属集合时，将路径上的所有节点直接指向根节点
```python
p = [i for i in range(n)]  #初始化父节点数组
size = [1] * n  #维护集合个数(只保证根节点的size有意义)
  
def find(x):  #返回根节点 + 路径压缩
    if p[x] != x:  
        p[x] = find(p[x])  
    return p[x]  
  
p[find(a)] = find(b)  #合并
size[find(b)] += size[find(a)]  #维护合并后的size

if find(a) == find(b):  #判断是否在同一节点
    print('Yes') 
```

### 2.2二叉树
```python
#完全二叉树
tree[2 ** i - 1:2 ** i + 2 ** i - 1]  #求每层所有节点切片(i为深度)
int(math.log(n, 2)) + 1  #求树深度(n为节点数)

```


## 3.搜索与图论
### 3.1组合
LeetCode 77.组合
```python
class Solution(object):  
    def combine(self, n, k):  
        """  
        :type n: int        
        :type k: int        
        :rtype: List[List[int]]  
        """        
        res = []  
        path = []  
  
        def dfs(n, k, stIndex):  
            if len(path) == k:  
                res.append(path[:])  
                return  
            for i in range(stIndex, n - (k - len(path)) + 2):  
                path.append(i)  
                dfs(n, k, i + 1)  
                path.pop()  
  
        dfs(n, k, 1)  
        return res
```

### 3.2DFS
**1.树的直径**
树上任意两节点之间最长的简单路径为树的直径
方法：两次DFS
1. 从任意节点 y 开始进行一次DFS，到达距离最远的节点 z 为直径的一端
2. 然后从 z 再进行一次DFS
ps：若需要记录直径的路径，在第二次DFS时记录每个节点的前序节点
```python
n = int(input())  
tree = [list() for _ in range(n + 1)]  
deep = [0] * (n + 1)  
for i in range(1, n):  
    p, q, d = map(int, input().split())  
    tree[p].append((q, d))  
    tree[q].append((p, d))  
st = 0  
  
  
def dfs(node, pa):  
    global st  
    for p, cos in tree[node]:  
        if p == pa:  
            continue  
        deep[p] = deep[node] + cos  
        if deep[p] > deep[st]:  
            st = p  
        dfs(p, node)  
  
  
dfs(1, 0)  
deep[st] = 0  
dfs(st, 0)  
print(deep[st])
```


## 4.数学知识
### 4.1质数
质数（素数）：在大于1的整数中，只包含1和本身两个约数
**判断质数：**
**1.试除法**
```python
def is_prime(n):
    if n < 2:
        return False;
    for i in range(2, int(n**0.5) + 1):
        if n % i == 0:
            return False
    return True
```
**2.埃氏筛法**
筛倍数
```python
#cnt：总质数
#primes：存储已筛出的质数
#st：标记删除的数
def is_prime(n):
    primes = []
    cnt = 0
    st = [False] * (n + 1)
    for i in range(2, n + 1):
        if not st[i]:
            primes.append(i)
            cnt += 1
            for j in range(i + i, n + 1, i):
                st[j] = True
    print(cnt)
```
**3.线性筛法O(n)!!**
n只会被其最小质因子筛掉
```python
def is_prime(n):
    primes = []
    st = [False] * (n + 1)
    for i in range(2, n + 1):
        if not st[i]:
            primes.append(i)
        for p in primes:
            if i * p > n:
                break
            st[i * p] = True
            if i % p == 0:
                break
    return primes
```
**分解质因数：**
**1.试除法**
```python
#i为底数，s为指数
def divide(n):
    for i in range(2, int(n**0.5) + 1):
        if n % i == 0:
            s = 0
            while n % i == 0:
                n //= i
                s+=1
            print(f'{i} {s}')
    if n > 1:
        print(f'{n} {1}')
```
### 4.2约数
**1.试除法求所有约数**
```python
def get_divisors(n):
    divisors = []
    for i in range(1, int(n**0.5) + 1):
        if n % i == 0:
            divisors.append(i)
            if i != n // i:
                divisors.append(n // i)
    return sorted(divisors)
```
**2.求约数的个数、约数之和**
3. 对n进行分解质因数：
   $$
   n={p_1}^{\alpha_1}{p_2}^{\alpha_2}\dots{p_n}^{\alpha_n}
   $$
4. 约数个数：
   $$
   (\alpha_1+1)(\alpha_2+2)\dots(\alpha_n+n)
   $$
5. 约数之和：
   $$
   (p_1^0+p_1^1+\dots+p_1^{\alpha _1})\dots (p_k^0+p_k^1+\dots+p_k^{\alpha _1})
   $$
- 如果n是乘积形式，则可分解每一项，然后将每一个质因子的指数上标相加
eg.给n个正整数，输出这些数乘积的约数的个数
```python
n = int(input())
primes = {}
while n > 0:
    n -= 1
    x = int(input())
    i = 2
    for i in range(2, x // i + 1):
        while x % i == 0:
            x //= i
            primes[i] = primes.get(i, 0) + 1
    if x > 1:
        primes[x] = primes.get(x, 0) + 1
res = 1
mod = 1e9 + 7

#求约数个数
for v in primes.values():
    res = res * (v + 1) % mod
print(res)

#求约数之和
res = 1
for k, v in primes.items():
    p = k
    a = v
    t = 1
    for i in range(a + 1, 1, -1):
        t = (t * p + 1) % mod
    res = res * t % mod
print(res)
```
**2.欧几里得算法（辗转相除法）**
求最大公约数
$$
\begin{array}{}
d\mid a,d\mid b \Leftrightarrow d\mid ax+by\\
(a,b)=(b,a\; mod\; b)
\end{array}
$$
```python
def gcd(a, b):
    return gcd(b, a % b) if b else a

#AIP
math.gcd(a,d)
```

## 5.动态规划
多决策问题
- 最后一步可由前面哪些状态转移过来⇒状态转移方程
- 与哪些参数有关⇒定义几维数组表示当前状态
- 时间复杂度⇒优化
找到最后一步→**去掉最后一步**

**步骤：**
6. dp数组以及下标含义
7. 递推公式
8. dp数组如何初始化
9. 遍历顺序
10. 打印dp数组(出问题后检测)

二维背包dp\[i]\[j]:
\[0, i]物品任取放容量为j的背包
不放物品i：dp\[i-1]\[j]
放物品i：dp\[i-1]\[j - weight\[i]] + value\[i]
dp\[i]\[j] = max{dp\[i-1]\[j], dp\[i-1]\[j - weight\[i]] + value\[i]}
### 01背包
每个物品只能使用一次
**方法：**
- 先遍历物品，后遍历背包
- 背包倒序遍历(防止重复)
dp\[j]：容量为j的背包的最大价值
递推公式：dp\[j] = max{dp\[j], dp\[j - weight\[i]] + value\[i]}
初始化：dp\[0] = 0
**应用：**
- 装满容器的最大价值(纯01背包)
- 给容器求能否装满
- 给定容器，最多能装多少
- 装满背包有多少种方法
- 装满背包最多有多少个物品
```java
	for(int i = 0;i<weightSize;i++) {
		for (int j = bagWeight; j >= weight[i]; j--) {
			dp[j] = max(dp[j], dp[j - wight[i]] + value[i]);
		}
	}
```
### 完全背包
同一件物品可使用无限次
**方法：**
- 背包与物品遍历顺序可以颠倒
    先物品后背包：组合数
    先背包后物品：排列数
- 背包顺序遍历(多次拿取物品)
**应用：**
- 装满背包的方法数
- 装满背包所需最少的物品数量
```java
	for(int i = 0;i<weightSize;i++) {
		for (int j = weight[i]; j <= bagWeight; j++) {
			dp[j] = max(dp[j], dp[j - wight[i]] + value[i]);
		}
	}
```
### 打家劫舍
偷房间内的金钱，相邻的房间不能偷
Ⅰ：房间线性
Ⅱ：房间连环
Ⅲ：房间为二叉树(树形dp)

### 股票问题
Ⅰ：一支股票买卖一次，不可在同一天出售
Ⅱ：一支股票，任何时候最多持有一股股票，可在同一天出售，可买卖多次
Ⅲ：一支股票，最多可以完成两笔交易，不能同时参与多笔交易
Ⅳ：一支股票，最多可以完成 k 笔交易，不能同时参与多笔交易
Ⅴ：在Ⅱ的基础上，卖出股票后无发在第二天买入股票
Ⅵ：在Ⅱ的基础上，每笔交易需要付手续费

### 子序列问题
- 最长递增子序列
- 最长连续递增子序列
- 最长重复子数组
- 最长公共子序列
- 最大子数组的和
- 




## 6.贪心
acwing 905
将所有区间按右端点排序，枚举每一个区间
```python
n = int(input())  
arr = []  
for i in range(n):  
    arr.append(tuple(map(int, input().split())))  
arr = sorted(arr, key=lambda x: x[1])  
res = 0  
ed = float('-inf')  
for a in arr:  
    if a[0] > ed:  
        res += 1  
        ed = a[1]  
print(res)
```

acwing 908
代码同上

acwing 906
将所有区间按左端点排序，枚举每一个区间，判断能否放到现有的组中（L\[i\] > Max_r)。若不存在则开新组，再放进去；若存在，则直接放进去，并更新当前组Max_r
```python
import heapq  
  
n = int(input())  
arr = []  
for i in range(n):  
    arr.append(tuple(map(int, input().split())))  
arr = sorted(arr, key=lambda x: x[0])  
heap = []  
for a in arr:  
    if not heap or heap[0] >= a[0]:  
        heapq.heappush(heap, a[1])  
    else:  
        heapq.heappop(heap)  
        heapq.heappush(heap, a[1])  
print(len(heap))
```

acwing 907
1.将所有区间按左端点从小到大排序
2.从前往后依次枚举每个区间，在能覆盖start的区间中，选择右端点最大的区间，然后将start更新成右端点的最大值
```python
st, end = map(int, input().split())
n = int(input())
arr = []
for i in range(n):
    arr.append(tuple(map(int, input().split())))
arr = sorted(arr, key=lambda x: x[0])
res = 0
success = False
for i in range(n):
    j = i
    r = float('-inf')
    while j < n and arr[j][0] <= st:
        r = max(r, arr[j][1])
        j += 1
    if r < st:
        res = -1
        break
    res += 1
    if r >= end:
        success = True
        break
    st = r
    i = j - 1
if not success:
    res = -1
print(res)
```

acwing 148
Huffman问题：n个数，每次合并最小的两个数为一个数，重复上述步骤
```python
import heapq  
  
n = int(input())  
arr = list(map(int, input().split()))  
heapq.heapify(arr)  
res = 0  
while len(arr) > 1:  
    a = heapq.heappop(arr)  
    b = heapq.heappop(arr)  
    res = res + a + b  
    heapq.heappush(arr, a + b)  
print(res)
```

acwing 913
从小到大排序，总时间最小
证明：调整法，假设某两个元素的排序不满足（类似递推方法）
```python
n = int(input())  
arr = list(map(int, input().split()))  
arr.sort()  
res = 0  
for i in range(n):  
    res += arr[i] * (n - i - 1)  
print(res)
```

acwing 104
```python
n = int(input())  
arr = list(map(int, input().split()))  
arr.sort()  
res = 0  
for i in range(n):  
    res += abs(arr[i] - arr[n // 2])  
print(res)
```

acwing 125
按照 重量+承重 从小到大的排序
```python
n = int(input())  
arr = []  
for i in range(n):  
    t = tuple(map(int, input().split()))  
    arr.append(t)  
arr = sorted(arr, key=lambda x: x[0] + x[1])  
res = float('-inf')  
sum = 0  
for i in range(n):  
    res = max(res, sum - arr[i][1])  
    sum += arr[i][0]  
print(res)
```

## Else
同余定理：两个数对k的余数相同，则两个数之差是k的倍数


## Attention
11. 
	- `arr.sort()`：直接将arr排序，返回值为空
	- `arr = sorted(arr)`：返回排序后的arr
	- `//`为整除，结果为**整数**
	- `/`为除，结果为**浮点数**
	- `<list>.append(item)`直接修改list并**返回None**

## API
```python
math.ceil(<number>)  #取上整
math.floor(<number>)  #取下整

round(<number>, <i>)  #返回四舍五入i位的小数

<list>.copy()  #浅拷贝，如果列表中的元素为列表、字典等可变对象元素时，仍指向原列表的对象
<list>.deepcopy()  #深拷贝，完全拷贝出新列表
<list>.index(<item>)  #返回列表中第一个item的下标
```

**双端队列**
```python
from collections import deque  
d = deque()
d.append(<num>)  #右端添加
d.appendleft(<num>)  #左端添加

d.pop()  # 移除右端元素
d.popleft()  #移除左端元素
```

**堆**
```python
import heapq  
heapq.heapify(<list>)  #将列表转换为堆
heapq.heappop(<list>)  #弹出堆顶
heapq.heappush(<list>, <num>)  #将num压入堆
```

**Hash**
```python
#python字典是基于hash实现的
<dic>.get(<key>, <default_key>)
<dic>.items()
<dic>.keys()
<dic>.values()

#使用set(无序)
s = {1, 2, 3}
s = set()
<set>.add(<item>)  #添加元素
<set>.remove(<item>)  #去除元素
<set2>.update(<set1>)  #将set1加入set2中
<item> in <set>  #检查元素在set中
<set1> | <set2>  #并集
<set1> & <set2>  #交集
<set1> - <set2>  #差集
```
- list是可变的，无法加入set

**Str**
```python
<str>.find(<s>)  #在<str>中寻找<s>下标，找不到返回-1
chr(<int>)  #ASCII → char
ord(<str>)  #char → ASCII
```
