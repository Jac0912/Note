# Alogrithm

## 前缀和

```java
int []a=new int[10];
int []sum=new int[11];
for(int i=1;i<11;i++)
{
    sum[i]=a[i-1]+sum[i-1];
}
```

## 差分

**改变数组多个区间**  $O(1)$

exp：区间L,R加a

- 差分数组：save[0]=arr[0] (定基数)    save[i]=arr[i]-arr[i-1] 
- save[L]+=a  save[R+1]-=a(消除对区间外的影响)
- 对save数组求前缀和得结果

## 获取数组中最大值

```java
int max=Arrays.stream(save).max().getAsInt();
```

## 数组排序

```java
Arrays.sort(array,Comparator.comparingInt(arr->arr[1]));

Arrays.sort(partic, Comparator.comparingInt((int[] arr) -> arr[1]).reversed());//降序

Arrays.sort(partic,new Comparator<int[]>() {
			@Override
			public int compare(int[] o1, int[] o2) {
				if(o1[1]==o2[1])
				{
					return Integer.compare(o1[0],o2[0]);  //同向升序，反向逆序
				}
				else {
					return Integer.compare(o1[1],o2[1]);
				}
				
			}
		}.reversed());
```

## 递归

**分析：**递归搜索树

n=30

## DFS

用递归实现n层for循环搜索(某种程度的枚举)

#### 枚举

1. 组合
2. 排列
3. 指数(方法间没关联)

### 指数

![image-20240302203637573](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240302203637573.png)

```java
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

public class Main {
	private static List<List<Integer>> res = new ArrayList<>();
	private static int n;

	public static void main(String[] args) {
		Scanner input = new Scanner(System.in);
		n = input.nextInt();
		List<Integer> temp = new ArrayList<>();
		dfs(0, temp);
		System.out.println(res.size());
		for (int i = 0; i < res.size(); i++) {
			for (int j = 0; j < 10; j++) {
				System.out.print(res.get(i).get(j) + " ");
			}
			System.out.println();
		}
	}

	private static void dfs(int sum, List<Integer> temp) {
		if (sum > n) {
			return;
		}
		if (temp.size() == 10) {
			if (sum == n) {
				res.add(new ArrayList<>(temp));
				return;
			}
			return;
		}

		for (int i = 1; i <= 3; i++) {
			sum += i;
			temp.add(i);
			dfs(sum, temp);
			sum-=temp.get(temp.size()-1);
			temp.remove(temp.size() - 1);
			
		}
	}
}
```





### 全排列

```java

import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

public class Test {
	static List<List<Integer>> list = new ArrayList<>();

	public static void main(String[] args) {
		Scanner input = new Scanner(System.in);
		int n = input.nextInt();
		int[] v = new int[n + 1];
		List<Integer> t = new ArrayList<>();
		dfs(n, v, t);
		for (int i = 0; i < list.size(); i++) {
			for (int j = 0; j < list.get(i).size(); j++) {
				System.out.print("    " + list.get(i).get(j));
			}
			System.out.println();
		}
	}

	public static void dfs(int n, int[] v, List<Integer> t) {
		if (t.size() == n) {
			list.add(new ArrayList<>(t));
			return;

		}
		for (int i = 1; i <= n; i++) {
			if (v[i] == 1) {
				continue;
			}
			v[i] = 1;
			t.add(i);
			dfs(n, v, t);
			v[i] = 0;
			t.remove(t.size() - 1);

		}
	}
}
```

![image-20240304212901222](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240304212901222.png)

```java



import java.io.*;
public class Test2 {
    static int n,m;
    static int N=10010;
    static int a[]=new int[N];
    static int res=0;
    static boolean flag=false;
    static boolean visited[]=new boolean[N];
    public static void main(String[] args) throws IOException{
        StreamTokenizer st=new StreamTokenizer(new BufferedReader(new InputStreamReader(System.in)));
        st.nextToken();
        n=(int)st.nval;
        st.nextToken();
        m=(int)st.nval;
        for(int i=1;i<=n;i++){
            st.nextToken();
            a[i]=(int)st.nval;
        }
        dfs(1);
    }
    static void dfs(int x){
        //减枝
        //x为搜索层数
        if(flag) return;
        if(x>n){
            res++;
            if(res==m+1){
                flag=true;
                for(int i=1;i<=n;i++){
                    System.out.print(a[i]);
                    System.out.print(" ");
                }
            }
            return;
        }
        for(int i=1;i<=n;i++){
            if(res==0) i=a[x];//最开始的情况
            if(!visited[i]){
                a[x]=i;
                visited[i]=true;
                dfs(x+1);
                a[x]=0;
                visited[i]=false;
            }
        }
    }
}
```



### 图(DFS)

![image-20240227211655135](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240227211655135.png)

````java
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Scanner;
import java.util.Set;

public class Test2 {
	static boolean flag = false;
	static List<Integer>[] list;

	public static void main(String[] args) {
		Scanner input = new Scanner(System.in);
		int n = input.nextInt();
		int m = input.nextInt();
		list = new List[n + 1];
		for (int i = 0; i <= n; i++) {
			list[i] = new ArrayList<>();
		}
		for (int i = 0; i < m; i++) {
			int x = input.nextInt();
			int y = input.nextInt();
			list[x].add(y);
			list[y].add(x);
		}

		int x = input.nextInt();
		int y = input.nextInt();
		flag = false;
		Set<Integer> set = new HashSet<>();
		set.add(x);
		dfs(x, y, x, 0, set);
		if (!flag) {
			System.out.println(-1);
		} else {
			int ans = 0;
			for (int i = 1; i <= n; i++) {
				if (i == x || i == y || list[i].size() == 0) {
                    //起始点、目标点、孤立点不可能是关键点
					continue;
				}
				set = new HashSet<>();
				set.add(x);
				flag = false;
				dfs(x, y, x, i, set);
				if (!flag) {
					ans++;
				}
			}
			System.out.println(ans);
		}
	}
	public static void dfs(int s, int t, int u, int i, Set<Integer> set) {
        //s:起始点  t：目标点  u：当前点  i：屏蔽点  set：回撤
		if (u == t) {
			flag = true;
			return;
		}
		for (int x : list[u]) {
			if (set.contains(x) || x == i) {
				continue;
			}
			set.add(x);
			dfs(s, t, x, i, set);
		}
	}
}
````

### 剪枝

在dfs开头添加条件提前终止不需要的路径

![image-20240228205505857](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240228205505857.png)

```java
import java.util.Scanner;

public class Main {
	
	private static int n,k,ans=0;
	public static void main(String[] args) {
		Scanner input=new Scanner(System.in);
		n=input.nextInt();
		k=input.nextInt();
		dfs(0,0,1);
		System.out.println(ans);
		
	}
	
	private static void dfs(int sum,int part,int now) {
		if(part==k)
		{
			if (sum==n) {
				ans++;
			}
			return;
		}
		for(int i=now;sum+i*(k-part)<=n;i++)
		{
			dfs(sum+i,part+1,i);
		}
	}

}
```







### 转换大小写：x^=32

## 同余定理

**a%k=b%k⇒|a-b|=n*k**

![image-20240220090630610](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240220090630610.png)

```java
import java.util.Scanner;
public class Test {
	
	public static void main(String[] args) {
		Scanner input=new Scanner(System.in);
		int N=input.nextInt();
		int K=input.nextInt();
		long[]sum=new long[N];
		long ans=0;
		for(int i=0;i<N;i++)
		{
			ans+=input.nextLong();
			sum[i]=ans;
		}
		int []save=new int[K];
		save[0]=1;
		long result=0;
		for(int i=0;i<N;i++)
		{
			int rem=(int)(sum[i]%K);    //(int)sum[i]%K为错，sum[i]为long可能>int
			result+=save[rem];
			save[rem]++;
		}
		System.out.println(result);
	}
}
```

## 二分查找

### King

![image-20240229165813532](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240229165813532.png)

```java
		int left = -1;
		int right = N;
		while (left + 1 != right) {
			int mid = (left + right) / 2;
			if (isBlue(mid)) {
				left = mid;
			} else {
				right = mid;
			}
			return right; // 或left(依情况定)
```

1. **建模：**划分红蓝区域
2. 找蓝色区域数据的性质
3. 确定返回值的位置（left or right）
4. 判断没找到的情况

### API

```java
Arrays.binarySearch(arr, target);
```

**使用时需排序**

### 查找>=k的最小值

```java 
int[] arr = { 1, 2, 3, 4, 5, 6 };
		int left = 0, right = 5; // n-1
		int k = 3;
		while (left < right) {
			int mid = (left + right) / 2;
			if (arr[mid] >= k) {
				right = mid;
			} else {
				left = mid + 1;
			}
		}

public static int downK(List list, int key){
    while (low < high) {
                //这里进行的是取低位， 也是为了使得循环可以正确退出，防止死循环
		int mid = (low + high)/2;
		if (a[mid] < key) {
		    low = mid +1;
		} else { //a[mid] >= key
		    high = mid;   //因为mid也满足情况
		}
	}
            //这里进行检查的原因参考上面的标注
	if (a[high] >= key) {
		return high;
	} else {
	    return -1;
	}
}
```

### 查找<=k的最大值

```java
int[] arr = { 1, 2, 3, 4, 5, 6 };
		int left = 0, right = 5; // n-1
		int k = 3;
		while (left < right) {
			int mid = (left + right+1) / 2;
			if (arr[mid] > k) {
				right = mid-1;
			} else {
				left = mid;
			}
		}

    //获取值<=k的最大值
    public static int uperK(int[] a, int k){
        int l = 0;
        int r = a.length - 1;
        //标注1： 这里是l<r，
        while(l < r){
            //标注2： 这样的操作是为了取高位
            int mid = (l + r + 1) / 2;
            if(a[mid] <= k) { //标注3：因为a[mid]<=k,所以a[mid]可能=k，所以mid坐标也满足条件，l = mid而不是mid+1;
                l = mid;
            }else{
                r= mid - 1; //这是a[mid] > k的时候。
            }
        }
        //标注4： 因为此时求得到的是最接近于目标值k的数，
        // 如果最小值都大于k的话，那么就没有办法得到了，所以就进行一个判断
        if(a[l] > k) return -1;
        //标注5： 其实这里无论返回 a[l] 还是a[r]都行，循环的退出时间是l == r 的时候
        return a[l];
    }
```

### 浮点二分

```java
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

```java
Collections.binarySearch(); 方法
```

## BigInteger

```java
BigInteger a=new BigInteger(String str);
BigInteger.valueOf(long x);

a.add(b);
a.subtract(b);
a.multiply(b);
a.divide(b);

a.max(b);
a.min(b);
BigInteger[]test=a.divideAndRemainder(b);  //test[0]为商，test[1]为余数
a.equals(b);
a.gcd(b);                                  //a,b最大公约数

LCM(a, b) = (a * b) / GCD(a, b)            //求最小公倍数
```

## 动态规划

### 线性dp

多决策问题

- 最后一步可由前面哪些状态转移过来⇒状态转移方程
- 与哪些参数有关⇒定义几维数组表示当前状态
- 时间复杂度⇒优化

找到最后一步→**去掉最后一步**

### 步骤

1. dp数组以及下标含义
2. 递推公式
3. dp数组如何初始化
4. 遍历顺序
5. 打印dp数组(出问题后检测)



## Math

### 最大公约数（gcd）

```java 
public static long gcd(long x, long y) {
		return y == 0 ? x : gcd(y, x % y);
	}
```

1. 较大数除以较小数，得余数
2. 较小数与余数作为新的两个数，重复1，直到余数为0
3. 余数为0时，除数即为两数最大公约数

### 最小公倍数（lcm）

```java
public static long lcm(long x,long y) {
		return x/gcd(x,y)*y;            //防止溢出
	}
```

### 进制转换

```java
String str=Integer.toString(target,int radix);    //target:待转换数，radix=进制
long out=Integer.parseInt(String x,int radix);    //待转换字符串
```

- 转换后字符串中的字母字符为小写

## 数据规模与算法

1.n<=30,指数级别，dfs+剪枝、状态压缩dp；

2.n<=100,O(n^3),floyd,dp，高斯消元；

3.n<=1000,O(n^2),O(n^2*logn)、dp、二分，朴素版Dijkstra、朴素版Prim、Bellman-Ford；

4.n<=10000,O(n*√n),块状链表、分块、莫队；

5.n<=100000,O(nlogn),各种sort、线段树、树状数组、set/map、heap、拓扑排序、dijkstra+heap、prim+heap、Kruskal、spfa、求凸包、求半平面交、二分、CDQ分治、整体二分、后缀数组、树链剖分、动态树；

6.n<=1000000,O(n),以及常数较小的O(nlogn)算法，单调队列、hash、双指针扫描、BFS、并查集、kmp、AC自动机；常数较小的O(nlogn)做法：sort、树状数组、heap、dijkstra、spfa；

7.n<=10000000,O(n),双指针扫描、kmp、AC自动机、线性筛素数；

8.n<=10^9,O(√n)，判断质数；

9.n<=10^18,O(logn),最大公约数、快速幂、数位DP；

10.n<=10^1000,O((logn)^2),高精度加减乘除；

11.n<=10^100000,O(logk*loglogk),k表示位数，高精度加减、FFT/NTT.

**int最大：2,147,483,647**













# Key~

- 最大公约数

```java
import java.math.BigInteger;

public class GCDExample {
    public static void main(String[] args) {
        BigInteger num1 = BigInteger.valueOf(24);
        BigInteger num2 = BigInteger.valueOf(36);
        
        BigInteger gcd = num1.gcd(num2);
        
        System.out.println("24和36的最大公约数是：" + gcd);
    }
}
```

- 读取

```java
Scanner input = new Scanner(System.in);
		String save = "";
		while (save.indexOf("E") == -1) {
			save += input.next();
		}
		char[] targ = save.substring(0, save.indexOf("E")).toCharArray();

while(！input.hasNext("\n"))  //当读到回车停止读取
		{
			
		}
```









# 注！！

1.

```java
public class Test {
	public static void main(String[] args) {
		char[] fir = "20100102".toCharArray();
		StringBuilder temp = new StringBuilder(fir.toString());
		System.out.println(temp.toString());
	}
}
```

`fir.toString()` 返回的是字符数组的默认字符串表示，即字符数组对象的哈希码和类型信息，并不是字符数组中的实际内容

可以使用 `String` 类的构造函数或 `String.valueOf()` 方法



2.int数组最大开$10^7$



3.求和开long