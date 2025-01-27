# 算法 

## 0.输入输出

### 0.1多行输入，每行两个整数

```python
import sys
for line in sys.stdin:
    a, b = line.split(' ')
    print(int(a) + int(b))
    print()
```

### 0.2多组数据，每组第一行为n，之后输入n行两个整数

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

### 0.3若干行输入，每行输入两个整数，遇到特定条件终止

```python
import sys
while True:
    s = input().split()
    a, b= int(s[0]), int(s[1])
    if not a or not b:
        break
    print(a + b)
```

### 0.4若干行输入，遇到0终止，每行第一个数为N，表示本行后面N个数

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

### 0.5若干行输入，每行包括两个整数a、b，由空格分隔，每行输出后接一个空行
```python
while True:
    try:
        x, y = map(int, (input().split()))
        print(x + y)
        print()
    except:
        break
```

### 0.6多组n行数据，每行先输入一个整数N，然后在同一行内输入M个整数，每组输出之间输出一个空行

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

### 0.7多组测试样例，每组输入数据为字符串，字符用空格分隔，输出为小数点后两位

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

### 0.8多组测试数据，首先输入一个整数N，接下来N行每行输入两个整数a和b，读取输入数据到Map

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

#### 4.2约数

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
1. 对n进行分解质因数：
   $$
   n={p_1}^{\alpha_1}{p_2}^{\alpha_2}\dots{p_n}^{\alpha_n}
   $$
2. 约数个数：
   $$
   (\alpha_1+1)(\alpha_2+2)\dots(\alpha_n+n)
   $$
3. 约数之和：
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
```
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

## Attention
1. 
	- `arr.sort()`：直接将arr排序，返回值为空
	- `arr = sorted(arr)`：返回排序后的arr




