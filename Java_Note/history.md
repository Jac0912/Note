## Acwing

### 1114. 棋盘问题

```java

import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

public class Main {
	static List<Integer> x;
	static List<Integer> y;

	static int ans = 0,sum=0;

	public static void main(String[] args) {
		Scanner input = new Scanner(System.in);
		while (!input.hasNext("-1")) {
			int n = input.nextInt();
			int k = input.nextInt();
			x = new ArrayList<Integer>();
			y = new ArrayList<Integer>();

			boolean[] x_oc = new boolean[n];
			boolean[] y_oc = new boolean[n];
			for (int i = 0; i < n; i++) {
				char[] temp = input.next().toCharArray();
				for (int j = 0; j < n; j++) {
					if (temp[j] == '#') {
						x.add(j);
						y.add(i);
					}

				}
			}
			if (x.size() >= k) {
				dfs(0, n, k,x_oc,y_oc);
			}

			System.out.println(ans);
			ans = 0;
			sum=0;
		}
	}

	private static void dfs(int curr, int n, int k,boolean[] x_oc,boolean[] y_oc) {
		if(sum==k)
		{
			ans++;	
			return;
		}
		if(x.size()<k-sum)
		{
			return;
		}
		for (int i = curr; i < x.size(); i++) {
			int x_temp=x.get(i);
			int y_temp=y.get(i);
			if (!x_oc[x_temp] && !y_oc[y_temp]) {
				x_oc[x_temp] = true;
				y_oc[y_temp] = true;
				sum++;
			}else {
				continue;
			}
			dfs(i + 1,n, k, x_oc, y_oc);
			x_oc[x_temp] = false;
			y_oc[y_temp] = false;
			sum--;
		}
	}
}
```

### 2.01背包

#### dfs(TLE)

```java
import java.util.Scanner;

public class Main {
	static int N,V;
	static int res=0;
	static int[][]item;
	public static void main(String[] args) {
		Scanner input=new Scanner(System.in);
		N=input.nextInt();
		V=input.nextInt();
		item=new int [N][2];
		for(int i=0;i<N;i++)
		{
			item[i][0]=input.nextInt();
			item[i][1]=input.nextInt();
		}
		
		dfs(0,0,0);
		System.out.println(res);
	}
	private static void dfs(int curr, int sum_v,int sum_w) {
		
		for(int i=curr;i<N;i++)
		{
			int temp=sum_v+item[i][0];
			if(temp>V)
			{
				continue;
			}
			int value=sum_w+item[i][1];
			res=Math.max(res,value);
			dfs(i+1,temp,value);
            //是i+1,从遍历到的当前位置向后遍历
		}
	}
}
```

#### dp(AC)

```java
import java.util.Scanner;

public class Main {
	static int N,V;
	static int[][]item;
	public static void main(String[] args) {
		Scanner input=new Scanner(System.in);
		N=input.nextInt();
		V=input.nextInt();
		item=new int [N+1][2];
		int [][]dp=new int[N+1][V+1];
		for(int i=1;i<=N;i++)
		{
			item[i][0]=input.nextInt();
			item[i][1]=input.nextInt();
		}
		for(int i=1;i<=N;i++)
		{
			for(int j=1;j<=V;j++)        //依次选定物品，遍历背包体积
			{
				if(item[i][0]>j)
				{
					dp[i][j]=dp[i-1][j]; //若无法装，则继承
					continue;
				}
				dp[i][j]=Math.max(dp[i-1][j],dp[i-1][j-item[i][0]]+item[i][1]);
                //取拿或不拿的最优情况，求出局部最优解
			}
		}
		System.out.println(dp[N][V]);
	}

}
```

#### dp+滚动数组(一维dp)(AC)

```java
import java.util.Scanner;

public class Main {
	static int N,V;
	static int[][]item;
	public static void main(String[] args) {
		Scanner input=new Scanner(System.in);
		N=input.nextInt();
		V=input.nextInt();
		item=new int [N+1][2];
		int []dp=new int[V+1];
		for(int i=1;i<=N;i++)
		{
			item[i][0]=input.nextInt();
			item[i][1]=input.nextInt();
		}
		for(int i=1;i<=N;i++) {
			for(int j=V;j>0;j--) {
				if(j<item[i][0]) {
					continue;
				}
				dp[j]=Integer.max(dp[j-item[i][0]]+item[i][1],dp[j]);
			}
		}
		System.out.println(dp[V]);
	}

}
```











## 洛谷

### P1019

```java
import java.util.Scanner;

public class Main {
	static String[] str;
	static int []flag;
	static int ans;
	static int add;

	public static void main(String[] args) {
		Scanner input = new Scanner(System.in);
		int n = input.nextInt();
		str = new String[n];
		flag=new int[n];
		for (int i = 0; i < n; i++)
		{
			str[i] = input.next();
		}
		String pre=input.next();
		for(int i=0;i<n;i++)
		{
			if(str[i].indexOf(pre)==0)
			{
				flag[i]++;
				dfs(str[i],n,str[i].length());
				flag[i]--;
			}
		}
		System.out.println(ans);
		
	}

	private static void dfs(String st,int n,int sum) {
		for(int i=0;i<n;i++)
		{
			if(dragon(st,str[i])&&flag[i]!=2)
			{
				flag[i]++;
				dfs(str[i],n,sum+add);
				flag[i]--;
			}
		}
		ans=Math.max(ans,sum);
	}
	
	private static boolean dragon(String pre,String lst) {
		for(int i=pre.length()-1;i>0;i--)
		{
			if(lst.length()<=pre.length()-i)
			{
				continue;
			}
			int index=lst.indexOf(pre.substring(i));
			if(index==0)
			{
				add=lst.length()-pre.length()+i;
				return true;
			}
		}
		return false;
	}
}
```

### P1216

#### dfs+记忆化搜索

```java
import java.util.Arrays;
import java.util.Scanner;

public class Main {
	static int r;
	static int[][]tra;
	static int[][]visit;
	public static void main(String[] args) {
		Scanner input=new Scanner(System.in);
		r=input.nextInt();
		tra=new int[r+1][r+1];
		visit=new int[r+1][r+1];
		for(int i=1;i<=r;i++)
		{
			for(int j=1;j<=i;j++)
			{
				tra[i][j]=input.nextInt();
			}
		}
		int res=dfs(1,1);
		System.out.println(res);
	}
	private static int dfs(int x, int y) {
		if(x>r||y>r) {
			return 0;
		}
		if(visit[x][y]==0)  //记忆化搜索
		{
			visit[x][y]=Math.max(dfs(x+1,y),dfs(x+1,y+1))+tra[x][y];
		}
		
		return visit[x][y];
	}
}
```

#### 递推

$O(n*n)$

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Scanner;

public class Main {
	static int r;
	static List<List<Integer>>tra=new ArrayList<>();
	static List<Integer>visit=new ArrayList<>();     //可优化为一维
	public static void main(String[] args) {
		Scanner input=new Scanner(System.in);
		r=input.nextInt();
		for(int i=0;i<r;i++)
		{
			tra.add(new ArrayList<>());
		}
		for(int i=0;i<r;i++)
		{
			for(int j=0;j<=i;j++)
			{
				tra.get(i).add(input.nextInt());
			}
		}
		for(int i=0;i<=r;i++)
		{
			visit.add(0);
		}
		
		
		for(int i=r-1;i>=0;i--)
		{
			for(int j=0;j<=i;j++)
			{
				visit.set(j,Math.max(visit.get(j),visit.get(j+1))+tra.get(i).get(j));
                //状态转移方程
                //list内的值不可直接修改，需调用set函数赋值
			}
		}
		System.out.println(visit.get(0));
	}
}
```

### P8649

```java

import java.util.Scanner;

public class Main {
	
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
			int rem=(int)(sum[i]%K);
			result+=save[rem];
			save[rem]++;
		}
		System.out.println(result);
	}
}
```

## C语言网

### 3050:最长上升子序列

```java
import java.util.Scanner;
public class Main {
	public static void main(String[] args) {
		Scanner input = new Scanner(System.in);
		int N = input.nextInt();
		int[] save = new int[N];
		for (int i = 0; i < N; i++) {
			save[i] = input.nextInt();
		}
		int[] dp = new int[N];
		dp[0] = 1;
		int max = 1;
		for (int i = 1; i < N; i++) {
			dp[i] = 1;
			for (int j = 0; j < i; j++) {
				if (save[i] > save[j]) {
					dp[i] = Math.max(dp[i], dp[j] + 1);
				}
			}
			max=Math.max(dp[i],max);
		}
		
		System.out.println(max);

	}

}
```





## LeetCode

### 62. 不同路径

```java
class Solution {
	public int uniquePaths(int m, int n) {
		int[][] dp = new int[m][n];
		Arrays.fill(dp[0],1);
		for(int i=0;i<m;i++) {
			dp[i][0]=1;
		}
		for (int i =1 ; i < m; i++) {
			for (int j = 1; j < n; j++) {
				dp[i][j]=dp[i-1][j]+dp[i][j-1];
			}
		}
		return dp[m-1][n-1];
	}
}
```

### 63.不同路径Ⅱ

```java
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
    	int m=obstacleGrid.length;
    	int n=obstacleGrid[0].length;
    	int[][]dp=new int[m][n];
    	for(int i=0;i<m;i++) {
    		if(obstacleGrid[i][0]!=1) {
    			dp[i][0]=1;
    		}else {
				break;
			}
    	}
    	for(int i=0;i<n;i++) {
    		if(obstacleGrid[0][i]!=1) {
    			dp[0][i]=1;
    		}else {
				break;
			}
    	}
    	
    	for(int i=1;i<m;i++) {
    		for(int j=1;j<n;j++) {
                if(obstacleGrid[i][j]==1){
                    continue;
                }
    			dp[i][j]=dp[i-1][j]+dp[i][j-1];
    		}
    	}
    	return dp[m-1][n-1];
    }
}
```

### 343.整数拆分

```java
class Solution {
	public int integerBreak(int n) {
		int[] dp = new int[n + 1];
		dp[2] = 1;
		for (int i = 3; i <= n; i++) {
			for (int j = 1; j <= i / 2; j++) {
                //从1开始拆分,为避免重复,只循环到 i/2 处
                
				int a = Integer.max(j * (i - j), j * dp[i - j]);
                //取拆分2次、拆分>2次(dp[i]为i拆分后的结果)、当前拆分方法的最大值
                
				dp[i] = Integer.max(a, dp[i]);
			}
		}
		return dp[n];
	}
}
```

### 96.不同的二叉搜索树

```java
class Solution {
    public int numTrees(int n) {
    	int []dp=new int[n+1];
    	dp[0]=1;
    	for(int i=1;i<=n;i++) {
    		for(int j=1;j<=i;j++) {
    			dp[i]+=dp[j-1]*dp[i-j];
    		}
    	}
    	return dp[n];
    }
}
```

### 416.分割等和子集

#### dfs+剪枝+回溯

```java
public class Main {
    static boolean flag;
	public static void main(String[] args) {
		int []nums= {1,2,3,4,5,6,7};
		System.out.println(canPartition(nums));
		
	}
	public static boolean canPartition(int[] nums) {
		boolean[] visit = new boolean[nums.length];
		int tar = 0;
		for (Integer i : nums) {
			tar += i;
		}
		System.out.println(tar);
		if (tar % 2 != 0) {
			return false;
		}

		dfs( 0, nums,tar,visit);
		if(flag) {
			return true;
		}
		return false;
	}

	private static void dfs( int sum, int[] nums,int tar,boolean[]visit) {
		
		if (flag||sum>tar/2) {
			return;
		}
		if (sum == tar / 2) {
			flag = true;
			return;
		}

			for (int j = 0; j < nums.length; j++) {
				if (!visit[j]) {
					visit[j] = true;
					dfs( sum + nums[j], nums,tar,visit);
					visit[j] = false;
				}

			}
	}
}

```

