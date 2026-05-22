# 1. 二分
![697](image/Pasted%20image%2020260323215706.png)
## 1.1. 模板
![](image/Pasted%20image%2020260323215818.png)

# 2. 回溯
- 组合问题：N个数里面按一定规则找出k个数的集合
- 切割问题：一个字符串按一定规则有几种切割方式
- 子集问题：一个N个数的集合里有多少符合条件的子集
- 排列问题：N个数按一定规则全排列，有几种排列方式
- 棋盘问题：N皇后，解数独等等
## 2.1. 组合
### 2.1.1. 模板
- `ans`：存结果
- `tmp`：存临时结果
- `idx`：递归的层
- `for`：遍历本层的元素

```cpp
void backtracking(参数) {
    if (终止条件) {
        存放结果;
        return;
    }
    for (选择：本层集合中元素（树中节点孩子的数量就是集合的大小）) {
        处理节点;
        backtracking(路径，选择列表); // 递归
        回溯，撤销处理结果
    }
}
```

### 2.1.2. skill
- 剪枝：若剩余元素不足目标个数，直接返回
- 若有重复元素，防止产生重复 tmp 的方法
	- 元素可以排序的情况：
		```cpp
		private:
			void dfs() {
				...
				for (...) {
				/* visit == false 的时候说明现在处于树层，需要去重：
				*    因为以 nums[i] 开头的情况已经在第一个 nums[i] 都考虑了
				*  visit == true 的时候说明现在处于树枝，说明：
				*	  当前正在处理第一个 nums[i] 的情况，可以加入重复元素
				*/
					if (i > 0 && nums[i] == nums[i - 1] && visit[i] == false) continue;
					
					visit[i] = true;
					...
					dfs();
					visit[i] = false;
				}
			}
		public:
			// 在主函数中对目标 vector 排序
			// 向 dfs 函数中传入 visit 数组
			vector<int> visit(nums.size(), false);
		```
	- 元素不能排序：
		```cpp
		private:
			void dfs() {
			// 使用 set 对每一层(for)出现过的元素进行去重
				unordered_set<int> set;
				...
				for (...) {
					...
					set.emplace(nums[i]);
					dfs();
				}
			}
		```
## 2.2. 排列
- 使用 `visit` 数组标记已经取过的数

# 3. 动态规划
多决策问题
- 最后一步可由前面哪些状态转移过来⇒状态转移方程
- 与哪些参数有关⇒定义几维数组表示当前状态
- 时间复杂度⇒优化
找到最后一步→**去掉最后一步**

**步骤：**
1. dp 数组以及下标含义
2. 递推公式
3. dp 数组如何初始化
4. 遍历顺序
5. 打印 dp 数组(出问题后检测)

二维背包 dp\[i]\[j]:
\[0, i]物品任取放容量为 j 的背包
不放物品 i：dp\[i-1]\[j]
放物品 i：dp\[i-1]\[j - weight\[i]] + value\[i]
dp\[i]\[j] = max{dp\[i-1]\[j], dp\[i-1]\[j - weight\[i]] + value\[i]}
## 3.1. 01 背包
每个物品只能使用一次
**方法：**
- 先遍历物品，后遍历背包
- 背包倒序遍历(防止重复)
dp\[j]：容量为 j 的背包的最大价值
递推公式：dp\[j] = max{dp\[j], dp\[j - weight\[i]] + value\[i]}
初始化：dp\[0] = 0
**应用：**
- 装满容器的最大价值(纯 01 背包)
- 给容器求能否装满（dp\[target] ?= target）
- 给定容器，最多能装多少
- 装满背包有**多少种方法**（dp 数组为方法数，**递推公式不变**dp\[j] += dp\[j - nums\[i]]）
- 装满背包最多有多少个物品（dp 数组为物品数量，枚举物品：max(dp\[i], dp\[i - t]) + 1）
```java
	for(int i = 0;i<weightSize;i++) {
		for (int j = bagWeight; j >= weight[i]; j--) {
			dp[j] = max(dp[j], dp[j - wight[i]] + value[i]);
		}
	}
```
## 3.2. 完全背包
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
## 3.3. 打家劫舍
偷房间内的金钱，相邻的房间不能偷
Ⅰ：房间线性
```cpp
dp[0] = nums[0];
dp[1] = max(nums[0], nums[1]);

// max(不偷, 偷)
dp[i] = max(dp[i - 1], dp[i - 2] + nums[i]);
```
Ⅱ：房间连环
```cpp
// 1. 考虑头，不考虑尾
// 2. 考虑尾，不考虑头
// 3. 用 I 计算两次
```
Ⅲ：房间为二叉树(树形 dp)
```cpp
// dp[0]: *max money* when rob curr
// dp[1]: *max money* when not rob curr
vector<int> tree(TreeNode* node) {
    if (node == nullptr) return vector<int>{0, 0};
    vector<int> dp(2, 0);
    vector<int> l = tree(node->left);
    vector<int> r = tree(node->right);
    return vector<int> {
        l[1] + node->val + r[1],
        max(l[0], l[1]) + max(r[0], r[1])
    };
}
```
## 3.4. 股票问题
Ⅰ：一支股票买卖一次，不可在同一天出售
- dp 内描述的是持有/不持有股票的**状态**
```cpp
int maxProfit(vector<int>& prices) {
       int n = prices.size();
       
       // dp[i][0]: have stock (state)
       // dp[i][1]: not have stock
       vector<vector<int>> dp(n, vector<int> {0, 0});
       
       dp[0][0] = -prices[0];
       dp[0][1] = 0;
       
       for (int i = 1; i < n; i++) {
           // already buy, today buy
           dp[i][0] = max(dp[i - 1][0], -prices[i]);
           // already sell, today sell
           dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] + prices[i]);
       }
       
       return dp[n - 1][1];
   }
```
Ⅱ：一支股票，任何时候最多持有一股股票，可在同一天出售，可买卖多次
```cpp
dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] - prices[i]);
dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] + prices[i]);
```
Ⅲ：一支股票，最多可以完成两笔交易，不能同时参与多笔交易
```cpp
// dp[i][0]: first buy (state)
// dp[i][1]: first sell (state)
// dp[i][2]: second buy (state)
// dp[i][3]: second sell (state)
vector<vector<int>> dp(n, vector<int>(4, 0));

dp[0][0] = -prices[0];
dp[0][1] = 0;
dp[0][2] = -prices[0];
dp[0][3] = 0;
```
Ⅳ：一支股票，最多可以完成 k 笔交易，不能同时参与多笔交易
- `dp[i][0]` 作为辅助列
```cpp
// dp[i][0]: aux
// dp[i][odd]: buy stock
// dp[i][even]: sell stock
vector<vector<int>> dp(n, vector<int>(2 * k + 1, 0));
```
Ⅴ：在Ⅱ的基础上，卖出股票后无法在第二天买入股票
```cpp
// dp[i][0]: have stock (state)
// dp[i][1]: sell stock (state)
// dp[i][2]: sell stock (action)
// dp[i][3]: freeze
vector<vector<int>> dp(n, vector<int>(4, 0));
```
Ⅵ：在Ⅱ的基础上，每笔交易需要付手续费
- 在 II 的基础上减去手续费即可

## 3.5. 子序列问题
- 最长递增子序列
	- dp 定义：以 `num[i]` 结尾的最长递增子序列的长度
- 最长连续递增子序列
	- 在上一个基础上只比较相邻元素即可
- 最长重复子数组（子数组：元素之间连续）
	```cpp
	// dp[i][j]: max common len end by i - 1 nums1 and j - 1 nums2
	// init 0: 0 is illegal
	vector<vector<int>> dp(n + 1, vector<int>(m + 1, 0));
	
	if (nums1[i - 1] == nums2[j - 1]) {
		dp[i][j] = dp[i - 1][j - 1] + 1;
	}
	```
- 最长公共子序列 / 不相交的线（子序列：元素之间不一定连续）
	- 如果当前字符不相等，就取一个不考虑当前元素最长的子序列
	```cpp
	if (text1[i - 1] == text2[j - 1]) {
		dp[i][j] = dp[i - 1][j - 1] + 1;
	} else {
		dp[i][j] = max(dp[i][j - 1], dp[i - 1][j]);
	}
	```
- 最大子数组的和
	```cpp
	dp[i] = max(dp[i - 1] + nums[i], nums[i]);
	```
- 不同的子序列（s 在 t 中出现个数）
	```cpp
	// dp[i][j]: the num of s[0 : i) in t[0 : j)
	vector<vector<unsigned long long>> dp(n + 1, vector<unsigned long long>(m + 1, 0));
	
	// the num of ""(t) in ""(s) is 1
	 for (int i = 0; i <= n; i++) {
		 dp[i][0] = 1;
	 }
	 
	 for (int i = 1; i <= n; i++) {
	 	for (int j = 1; j <=m; j++) {
	 		if (s[i - 1] == t[j - 1]) {
	 			// two situation: curr / delete a letter
	 			dp[i][j] = dp[i - 1][j - 1] + dp[i - 1][j];
	 		} else {
	 			dp[i][j] = dp[i - 1][j];
	 		}
	 	}
	 }
	```
- 两个字符串删除操作（删除使得两个字符串相同）
	```cpp
	// dp[i][j]: the min step to let word1[0 : i) and word2[0 : j) same
    vector<vector<int>> dp(n + 1, vector<int>(m + 1, 0));
         
	 // the step to let word1[0 : i) and "" be same is i
	 for (int i = 0; i <= n; i++) {
		 dp[i][0] = i;
	 }
	 
	 for (int j = 0; j <= m; j++) {
		 dp[0][j] = j;
	 }
	 
	 for (int i = 1; i <= n; i++) {
		 for (int j = 1; j <= m; j++) {
			 if (word1[i - 1] == word2[j - 1]) {
				 dp[i][j] = dp[i - 1][j - 1];
			 } else {
				 // three situation: delete word1 / delete word2 / both delete
				 dp[i][j] = min({dp[i - 1][j] + 1, dp[i][j - 1] + 1, dp[i - 1][j - 1] + 2});
			 }
		 }
	 }
	```
- 编辑距离
	```cpp
	if (word1[i - 1] == word2[j - 1]) {
        dp[i][j] = dp[i - 1][j - 1];
    } else {
    	// delete / add is same; replace need special handling
    	dp[i][j] = min({dp[i][j - 1] + 1, dp[i - 1][j] + 1, dp[i - 1][j - 1] + 1});
    }
	```
- 回文子串
	- 动态规划：由中间向两端扩散，记录中间是否为回文子串（bool）
	- 双指针：以一个元素 / 两个元素为中心，向两端扩散，计算有几个回文子串
	```cpp
	// dp[i][j]: if the substr between i and j is palindrome string
    vector<vector<bool>> dp(n, vector<bool>(n, false));
    
	for (int i = n - 1; i >= 0; i--) {
		for (int j = i; j < n; j++) {
			if (s[i] == s[j]) {
				if (j - i <= 1) {
					// exp: "a" / "aa"
					dp[i][j] = true;
					res++;
				} else if (dp[i + 1][j - 1]) {
					dp[i][j] = true;
					res++;
				}
			}
		}
	}
	```
- 最长回文子序列
	```cpp
	// dp[i][j]: the max palindromic subsequence len between s[i, j]
    vector<vector<int>> dp(n, vector<int>(n, 0));
         
    for (int i = 0; i < n; i++) {
        dp[i][i] = 1;
    }
    
    for (int i = n - 1; i >= 0; i--) {
		for (int j = i + 1; j < n; j++) {
			 if (s[i] == s[j]) {
				 dp[i][j] = dp[i + 1][j - 1] + 2;
			 } else {
				 dp[i][j] = max(dp[i + 1][j], dp[i][j - 1]);
			 }
		 }
	 }
	```

# 4. keys
- 函数尽量使用按引用传递 `&`
- dfs 返回类型：
	- bool：搜索一个解（单个树枝），解数独
	- void：需要搜索多个解（多个树枝）
- `return res++` 返回的结果是 `res` 而不是 `res + 1`（先用值，再自增）
- 类型转换
	```cpp
	long tmp = (long)nums[i] + nums[j] + nums[l] + nums[r];
	```
	- 需要对第一个 nums 转换，不然会用默认的 int 先相加
# 5. STL
- 字符串与数字转换
	```cpp
	int num = 123;
	string str = to_string(num); // 123 -> "123"
	int num = stoi(str);         // "123" -> 123
	```
- 使用构造函数
	```cpp
	/**
	 * Definition for singly-linked list.
	 * struct ListNode {
	 *     int val;
	 *     ListNode *next;
	 *     ListNode() : val(0), next(nullptr) {}
	 *     ListNode(int x) : val(x), next(nullptr) {}
	 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
	 * };
	 */
	 
	 ListNode* dummy = new ListNode(0, head);
	```
- 排序
	```cpp
	sort(t.begin(), t.end()) // 从小到大
	sort(t.begin(), t.end(), [](int a, int b) {
		return a > b;
	})
	```
- 交换数组元素：`swap(arr[i], arr[j])`
- 累加：`accumulate(t.begin(), t.end(), 0)`
	- 不是 **sum**！
	- 需要在最后一个参数指定起加值
- 找最大元素：`max_element(nums.begin(), nums.end());`
	- 返回的是一个**指针类型**
	- 需要用 `*` 来获得值
- 提取 `<str>` 的子字符串：`<str>.substr(pos, len)`
	- `pos`：提取的起始下标
	- `len`：提取的长度
- 反转字符串：`reverse(str.begin(), str.end())`
- 重置 `vector` 大小：`<vector>.resize(num, in)`
	- `num`：填充 `in` 的个数
	- `in`：填充的内容（eg. `vector<bool>(s.size(), false)`）
- set / map
	```cpp
	// 哈希表
	unordered_map<K, V>
	unordered_set<K>
	
	<set>.insert(<item>)
	<set>.erase(<item>)
	<set>.count(<item>)  // 判断元素是否存在，返回 0 / 1
	<set>.emplace(<item>) //插入元素，或者用 insert 方法
	
	====
	// 红黑树，元素有序排列
	map<K, V>
	```
- stack
	```cpp
	stack<int> st;
	st.push(num);
	st.pop;
	st.top();
	st.empty();
	st.size();
	```
- queue
	```cpp
	queue<int> q;
	q.push(num);
	q.pop();
	q.front();
	q.back();
	q.empty();
	q.size();
	```

