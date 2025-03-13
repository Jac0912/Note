# LeetCode
LeetCode 416. 分割等和子集(01背包)
```python
class Solution(object):  
    def canPartition(self, nums):  
        """  
        :type nums: List[int]        
        :rtype: bool  
        """        
        s = sum(nums)  
        if s % 2 != 0:  
            return False  
        tar = s // 2  
        dp = [0] * (tar + 1)  
        for i in range(len(nums)):  
            for j in range(tar, nums[i] - 1, -1):  
                dp[j] = max(dp[j], dp[j - nums[i]] + nums[i])  
        if dp[-1] == tar:  
            return True  
        return False
```

LeetCode 1049. 最后一块石头的重量 II(01背包)
```python
class Solution(object):  
    def lastStoneWeightII(self, stones):  
        """  
        :type stones: List[int]        
        :rtype: int  
        """        
        s = sum(stones)  
        tar = s // 2  
        dp = [0] * (tar + 1)  
        for i in range(len(stones)):  
            for j in range(tar, stones[i] - 1, -1):  
                dp[j] = max(dp[j], dp[j - stones[i]] + stones[i])  
        return s - 2 * dp[-1]
```

LeetCode 494. 目标和(01背包)
dp\[j]：容量为j的背包的最大方法数
```python
class Solution(object):  
    def findTargetSumWays(self, nums, target):  
        """  
        :type nums: List[int]        
        :type target: int        
        :rtype: int  
        """        
        s = sum(nums)  
        if (s - target) < 0 or (s - target) % 2 != 0:  
            return 0  
        b = (s - target) // 2  
        dp = [0] * (b + 1)  
        dp[0] = 1  
        for i in range(len(nums)):  
            for j in range(b, nums[i] - 1, -1):  
                dp[j] += dp[j - nums[i]]  
        return dp[-1]
```

LeetCode 474. 一和零(01背包)
dp\[i]\[j]：i个0，j个1最多背dp\[i]\[j]物品个数
背包有两个维度
```python
class Solution(object):  
    def findMaxForm(self, strs, m, n):  
        """  
        :type strs: List[str]        
        :type m: int        
        :type n: int        
        :rtype: int  
        """        
        dp = [[0] * (n + 1) for _ in range(m + 1)]  #m行n列数组的分配方式
        for st in strs:  
            x = st.count('0')  
            y = st.count('1')  
            for i in range(m, x - 1, -1):  
                for j in range(n, y - 1, -1):  
                    dp[i][j] = max(dp[i][j], dp[i - x][j - y] + 1)  
        return dp[-1][-1]
```

LeetCode：518.零钱兑换 II(完全背包)
```python
class Solution(object):  
    def change(self, amount, coins):  
        """  
        :type amount: int        
        :type coins: List[int]        
        :rtype: int  
        """        
        dp = [0] * (amount + 1)  
        dp[0] = 1  
        for i in range(len(coins)):  
            for j in range(coins[i], amount + 1):  
                dp[j] += dp[j - coins[i]]  
        return dp[-1]
```

LeetCode：377. 组合总和 Ⅳ(完全背包)
```python
class Solution(object):  
    def combinationSum4(self, nums, target):  
        """  
        :type nums: List[int]        
        :type target: int        
        :rtype: int  
        """        
        dp = [0] * (target + 1)  
        dp[0] = 1  
        for i in range(target + 1):  
            for j in range(len(nums)):  
                if i >= nums[j]:  
                    dp[i] += dp[i - nums[j]]  
        return dp[-1]
```

LeetCode：377. 组合总和 Ⅳ(完全背包)
```python
class Solution(object):  
    def coinChange(self, coins, amount):  
        """  
        :type coins: List[int]        
        :type amount: int        
        :rtype: int  
        """        
        dp = [float('inf')] * (amount + 1)  
        dp[0] = 0  
        for i in range(amount + 1):  
            for j in range(len(coins)):  
                if i >= coins[j]:  
                    dp[i] = min(dp[i], dp[i - coins[j]] + 1)  
        if math.isinf(dp[-1]):  
            return -1  
        return dp[-1]
```

LeetCode：279. 完全平方数(完全背包)
dp\[j]：装满容量为j的背包所需最少的元素数量
```python
class Solution(object):  
    def numSquares(self, n):  
        """  
        :type n: int        
        :rtype: int  
        """        
        dp = [float('inf')] * (n + 1)  
        dp[0] = 0  
        for i in range(1, n + 1):  
            for j in range(i * i, n + 1):  
                dp[j] = min(dp[j], dp[j - i * i] + 1)  
        return dp[-1]
```

LeetCode：139. 单词拆分(完全背包)
dp\[i]：是否能组成长度为i的字符串
```python
class Solution(object):  
    def wordBreak(self, s, wordDict):  
        """  
        :type s: str        
        :type wordDict: List[str]        
        :rtype: bool  
        """        
        dp = [False] * (len(s) + 1)  
        dp[0] = True  
        for i in range(1, len(s) + 1):  
            for j in range(i):  
                if s[j:i] in wordDict and dp[j]:  
                    dp[i] = True  
        return dp[-1]
```

LeetCode：198. 打家劫舍
dp\[i]：考虑到下标i所偷最大的金币数量
```python
class Solution(object):  
    def rob(self, nums):  
        """  
        :type nums: List[int]        
        :rtype: int  
        """        
        l = len(nums)  
        if l == 1:  
            return nums[0]  
        dp = [0] * l  
        dp[0] = nums[0]  
        dp[1] = max(nums[0], nums[1])  
        for i in range(2, len(nums)):  
            dp[i] = max(dp[i - 2] + nums[i], dp[i - 1])  
        return dp[-1]
```

LeetCode：213. 打家劫舍 II
环形问题分不考虑头、尾两种情况讨论
```python
class Solution(object):  
    def rob(self, nums):  
        """  
        :type nums: List[int]        
        :rtype: int  
        """        
        def robLine(nums):  
            l = len(nums)  
            if l == 1:  
                return nums[0]  
            dp = [0] * l  
            dp[0] = nums[0]  
            dp[1] = max(nums[0], nums[1])  
            for i in range(2, len(nums)):  
                dp[i] = max(dp[i - 2] + nums[i], dp[i - 1])  
            return dp[-1]  
  
        l = len(nums)  
        if l == 1:  
            return nums[0]  
        head = robLine(nums[1:])  
        tail = robLine(nums[:-1])  
        return max(head, tail)
```

LeetCode：337. 打家劫舍 Ⅲ
dp\[0]：不偷当前节点所获的最大金钱
dp\[1]：偷当前节点所获的最大金钱
```python
# Definition for a binary tree node.  
# class TreeNode(object):  
#     def __init__(self, val=0, left=None, right=None):  
#         self.val = val  
#         self.left = left  
#         self.right = right  
class Solution(object):  
    def rob(self, root):  
        """  
        :type root: Optional[TreeNode]        
        :rtype: int  
        """        
        return max(self.robNode(root))  
  
    def robNode(self, node):  
        if not node:  
            return 0, 0  
        lF, lT = self.robNode(node.left)  
        rF, rT = self.robNode(node.right)  
        return max(lF, lT) + max(rF, rT), node.val + lF + rF
```

LeetCode：121. 买卖股票的最佳时机
dp\[i]\[0]：持有股票最大现金
dp\[i]\[1]：不持有股票最大现金
```python
class Solution(object):  
    def maxProfit(self, prices):  
        """  
        :type prices: List[int]        
        :rtype: int  
        """        
        l = len(prices)  
        dp = [[0, 0] for _ in range(l)]  
        dp[0][0] = -prices[0]  
        dp[0][1] = 0  
        for i in range(1, l):  
            dp[i][0] = max(-prices[i], dp[i - 1][0])  
            dp[i][1] = max(dp[i - 1][0] + prices[i], dp[i - 1][1])  
        return dp[-1][1]
```

LeetCode：122.买卖股票的最佳时机 II
```python
class Solution(object):  
    def maxProfit(self, prices):  
        """  
        :type prices: List[int]        
        :rtype: int  
        """        
        l = len(prices)  
        dp = [[0, 0] for _ in range(l)]  
        dp[0][0] = -prices[0]  
        dp[0][1] = 0  
        for i in range(1, l):  
            dp[i][0] = max(dp[i - 1][1] - prices[i], dp[i - 1][0])  
            dp[i][1] = max(dp[i - 1][0] + prices[i], dp[i - 1][1])  
        return dp[-1][1]
```

LeetCode：123.买卖股票的最佳时机Ⅲ
dp\[i]\[0]：第一次持有
dp\[i]\[1]：第一次不持有
dp\[i]\[2]：第二次持有
dp\[i]\[3]：第二次不持有
```python
class Solution(object):  
    def maxProfit(self, prices):  
        """  
        :type prices: List[int]        
        :rtype: int  
        """        
        length = len(prices)  
        dp = [[0] * 4 for _ in range(length)]  
        dp[0][0] = -prices[0]  
        dp[0][2] = -prices[0]  
        for i in range(1, length):  
            dp[i][0] = max(dp[i - 1][0], -prices[i])  
            dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] + prices[i])  
            dp[i][2] = max(dp[i - 1][2], dp[i - 1][1] - prices[i])  
            dp[i][3] = max(dp[i - 1][3], dp[i - 1][2] + prices[i])  
        return dp[-1][-1]
```

LeetCode：188. 买卖股票的最佳时机 IV
```python
class Solution(object):  
    def maxProfit(self, k, prices):  
        """  
        :type k: int        
        :type prices: List[int]        
        :rtype: int  
        """        
        length = len(prices)  
        dp = [[0] * (2 * k + 1) for _ in range(length)]  
        for i in range(1, 2 * k, 2):  
            dp[0][i] = -prices[0]  
        for i in range(1, length):  
            for j in range(0, 2 * k, 2):  
                dp[i][j + 1] = max(dp[i - 1][j + 1], dp[i - 1][j] - prices[i])  
                dp[i][j + 2] = max(dp[i - 1][j + 2], dp[i - 1][j + 1] + prices[i])  
        return dp[-1][-1]
```

LeetCode：309. 买卖股票的最佳时机含冷冻期
dp\[i]\[0]：持有股票
dp\[i]\[1]：保持卖出股票状态
dp\[i]\[2]：卖出股票
dp\[i]\[3]：冷冻期
```python
class Solution(object):  
    def maxProfit(self, prices):  
        """  
        :type prices: List[int]        
        :rtype: int  
        """        
        dp = [[0] * 4 for _ in range(len(prices))]  
        dp[0][0] = -prices[0]  
        for i in range(1, len(prices)):  
            dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] - prices[i], dp[i - 1][3] - prices[i])  
            dp[i][1] = max(dp[i - 1][1], dp[i - 1][3])  
            dp[i][2] = dp[i - 1][0] + prices[i]  
            dp[i][3] = dp[i - 1][2]  
        return max(dp[-1][1:])
```

LeetCode：714. 买卖股票的最佳时机含手续费
```python
class Solution(object):  
    def maxProfit(self, prices, fee):  
        """  
        :type prices: List[int]        
        :type fee: int        
        :rtype: int  
        """        
        dp = [[0, 0] for _ in range(len(prices))]  
        dp[0][0] = -prices[0]  
        for i in range(1, len(prices)):  
            dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] - prices[i])  
            dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] + prices[i] - fee)  
        return dp[-1][-1]
```

LeetCode：300. 最长递增子序列
dp\[i]：以nums\[i]为结尾的最长递增子序列的长度
```python
class Solution(object):  
    def lengthOfLIS(self, nums):  
        """  
        :type nums: List[int]        
        :rtype: int  
        """        
        dp = [1] * len(nums)  
        for i in range(1, len(nums)):  
            for j in range(i):  
                if nums[j] < nums[i]:  
                    dp[i] = max(dp[i], dp[j] + 1)  
        return max(dp)
```

LeetCode：674.最长连续递增序列
dp\[i]：以nums\[i]为结尾的最长连续递增子序列的长度
```python
class Solution(object):  
    def findLengthOfLCIS(self, nums):  
        """  
        :type nums: List[int]        
        :rtype: int  
        """        
        dp = [1] * len(nums)  
        for i in range(1, len(nums)):  
            if nums[i] > nums[i - 1]:  
                dp[i] = dp[i - 1] + 1  
        return max(dp)
```

LeetCode：718. 最长重复子数组
dp\[i]\[j]：以nums1\[i-1]结尾和nums2\[j-1]结尾的最长重复子数组的长度
```python
class Solution(object):  
    def findLength(self, nums1, nums2):  
        """  
        :type nums1: List[int]        
        :type nums2: List[int]        
        :rtype: int  
        """        
        dp = [[0] * (len(nums2) + 1) for _ in range(len(nums1) + 1)]  
        res = 0  
        for i in range(1, len(nums1) + 1):  
            for j in range(1, len(nums2) + 1):  
                if nums1[i - 1] == nums2[j - 1]:  
                    dp[i][j] = dp[i - 1][j - 1] + 1  
                res = max(res, dp[i][j])  
        return res
```

LeetCode：1143. 最长公共子序列
dp\[i]\[j]：nums1\[0, i-1]和nums2\[0, j-1]的最长公共子序列的长度
```python
class Solution(object):  
    def longestCommonSubsequence(self, text1, text2):  
        """  
        :type text1: str        
        :type text2: str        
        :rtype: int  
        """        
        dp = [[0] * (len(text2) + 1) for _ in range(len(text1) + 1)]  
        for i in range(1, len(text1) + 1):  
            for j in range(1, len(text2) + 1):  
                if text1[i - 1] == text2[j - 1]:  
                    dp[i][j] = dp[i - 1][j - 1] + 1  
                else:  
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])  
        return dp[-1][-1]
```

LeetCode：1035. 不相交的线
```python
class Solution(object):  
    def maxUncrossedLines(self, nums1, nums2):  
        """  
        :type nums1: List[int]        
        :type nums2: List[int]        
        :rtype: int  
        """        
        dp = [[0] * (len(nums2) + 1) for _ in range(len(nums1) + 1)]  
        for i in range(1, len(nums1) + 1):  
            for j in range(1, len(nums2) + 1):  
                if nums1[i - 1] == nums2[j - 1]:  
                    dp[i][j] = dp[i - 1][j - 1] + 1  
                else:  
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])  
        return dp[-1][-1]
```

LeetCode：53.最大子数组和
dp\[i]：以nums\[i]结尾的最大子序列的和
```python
class Solution(object):  
    def maxSubArray(self, nums):  
        """  
        :type nums: List[int]        
        :rtype: int  
        """        
        dp = [0] * (len(nums))  
        dp[0] = nums[0]  
        for i in range(1, len(nums)):  
            dp[i] = max(dp[i - 1] + nums[i], nums[i])  
        return max(dp)
```

LeetCode：392.判断子序列
dp\[i]\[j]：以 i-1 为结尾的 s 中有以 j-1 为结尾的 t 的相同子序列的长度
```python
class Solution(object):  
    def isSubsequence(self, s, t):  
        """  
        :type s: str        
        :type t: str        
        :rtype: bool  
        """        
        dp = [[0] * (len(t) + 1) for _ in range(len(s) + 1)]  
        for i in range(1, len(s) + 1):  
            for j in range(1, len(t) + 1):  
                if s[i - 1] == t[j - 1]:  
                    dp[i][j] = dp[i - 1][j - 1] + 1  
                else:  
                    dp[i][j] = dp[i][j - 1]  
        return dp[-1][-1] == len(s)
```

LeetCode：115. 不同的子序列
dp\[i]\[j]：以 i-1 为结尾的 s 中有以 j-1 为结尾的 t 的子序列的个数
```python
class Solution(object):  
    def numDistinct(self, s, t):  
        """  
        :type s: str        
        :type t: str        
        :rtype: int  
        """        
        dp = [[0] * (len(t) + 1) for _ in range(len(s) + 1)]  
        for i in range(len(s) + 1):  
            dp[i][0] = 1  
        for i in range(1, len(s) + 1):  
            for j in range(1, len(t) + 1):  
                if s[i - 1] == t[j - 1]:  
                    dp[i][j] = dp[i - 1][j - 1] + dp[i - 1][j]  
                else:  
                    dp[i][j] = dp[i - 1][j]  
        return dp[-1][-1]
```

LeetCode：583.两个字符串的删除操作
dp\[i]\[j]：以 i-1 为结尾word1和以 j-1 为结尾word2字符串相同的最小操作次数
```python
class Solution(object):  
    def minDistance(self, word1, word2):  
        """  
        :type word1: str        
        :type word2: str        
        :rtype: int  
        """        
        dp = [[0] * (len(word2) + 1) for _ in range(len(word1) + 1)]  
        for i in range(len(word1) + 1):  
            dp[i][0] = i  
        for j in range(len(word2) + 1):  
            dp[0][j] = j  
        for i in range(1, len(word1) + 1):  
            for j in range(1, len(word2) + 1):  
                if word1[i - 1] == word2[j - 1]:  
                    dp[i][j] = dp[i - 1][j - 1]  
                else:  
                    dp[i][j] = min(dp[i - 1][j] + 1, dp[i][j - 1] + 1, dp[i - 1][j - 1] + 2)  
        return dp[-1][-1]
```

LeetCode：72.编辑距离
dp\[i]\[j]：以 i-1 为结尾word1和以 j-1 为结尾word2字符串相同的最小操作次数
```python
class Solution(object):  
    def minDistance(self, word1, word2):  
        """  
        :type word1: str        
        :type word2: str        
        :rtype: int  
        """        
        dp = [[0] * (len(word2) + 1) for _ in range(len(word1) + 1)]  
        for i in range(len(word1) + 1):  
            dp[i][0] = i  
        for j in range(len(word2) + 1):  
            dp[0][j] = j  
        for i in range(1, len(word1) + 1):  
            for j in range(1, len(word2) + 1):  
                if word1[i - 1] == word2[j - 1]:  
                    dp[i][j] = dp[i - 1][j - 1]  
                else:  
                    dp[i][j] = min(dp[i - 1][j] + 1, dp[i][j - 1] + 1, dp[i - 1][j - 1] + 1)  
        return dp[-1][-1]
```

LeetCode：647.回文子串
dp\[i]\[j]：\[i, j]字串是否为回文字串
```python
class Solution(object):  
    def countSubstrings(self, s):  
        """  
        :type s: str        
        :rtype: int  
        """        
        res = 0  
        dp = [[False] * len(s) for _ in range(len(s))]  
        for i in range(len(s) - 1, -1, -1):  
            for j in range(i, len(s)):  
                if s[i] == s[j]:  
                    if j - i <= 1:  
                        dp[i][j] = True  
                        res += 1  
                    elif dp[i + 1][j - 1]:  
                        dp[i][j] = True  
                        res += 1  
        return res
```

LeetCode：516.最长回文子序列
dp\[i]\[j]：\[i, j]的最长回文子序列的长度
```python
class Solution(object):  
    def longestPalindromeSubseq(self, s):  
        """  
        :type s: str        
        :rtype: int  
        """        
        dp = [[0] * len(s) for _ in range(len(s))]  
        for i in range(len(s)):  
            dp[i][i] = 1  
        for i in range(len(s) - 1, -1, -1):  
            for j in range(i + 1, len(s)):  
                if s[i] == s[j]:  
                    dp[i][j] = dp[i + 1][j - 1] + 2  
                else:  
                    dp[i][j] = max(dp[i + 1][j], dp[i][j - 1])  
        return dp[0][-1]
```

LeetCode：34. 在排序数组中查找元素的第一个和最后一个位置
```python
import bisect  
class Solution(object):  
    def searchRange(self, nums, target):  
        """  
        :type nums: List[int]        
        :type target: int        
        :rtype: List[int]  
        """        
        if not nums:  
            return [-1, -1]  
        nums = sorted(nums)  
        l = bisect.bisect_left(nums, target)  
        r = bisect.bisect_right(nums, target)  
        if l == len(nums) or nums[l] != target:  
            return [-1, -1]  
        return [l, r - 1]
```

# 蓝桥杯
###### 2928.分糖果
```python
import math  
  
n, x = map(int, input().split())  
s = sorted(input())  
s = [''] + s  # 使下标从1开始  
if s[1] == s[x]:  
    if s[x + 1] == s[-1]:  
        print(s[x], end='')  
        print(s[x + 1] * (math.ceil((n - x) / x)), end='')  
    else:  
        print(''.join(s[x:]))  
else:  
    print(s[x])
```

###### 97.k倍区间（同余定理，前缀和）
```python
n, k = map(int, input().split())  
arr = [0] * n  
for i in range(n):  
    arr[i] = int(input())  
head = [0] * (n + 1)  
for i in range(1, n + 1):  
    head[i] = head[i - 1] + arr[i - 1]  
  
mod = [0] * n  
for i in range(1, n + 1):  
    mod[head[i] % k] += 1  
res = mod[0]  
for m in mod:  
    res += m * (m - 1) // 2  
print(res)
```

###### 99.分巧克力（二分）
```python
n, k = map(int, input().split())  
arr = [list(map(int, input().split())) for _ in range(n)]  
  
  
def check(edge):  
    ans = 0  
    for h, w in arr:  
        ans += (h // edge) * (w // edge)  
        if ans >= k:  
            return True  
    return False  
  
left = -1  
right = 100001  
while left + 1 != right:  
    mid = (left + right) // 2  
    if check(mid):  
        left = mid  
    else:  
        right = mid  
print(left)
```

###### 101.拉马车（模拟）
```python
from collections import deque  
  
a = deque(input())  
b = deque(input())  
  
mapp = {'A': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9, 'X': 10, 'J': 11, 'Q': 12, 'K': 13}  
flag = [False] * 14  
  
card = []  
turn = True  
while a and b:  
    if turn:  
        t = a.popleft()  
        if flag[mapp[t]]:  
            flag[mapp[t]] = False  
            a.append(t)  
            while True:  
                c = card.pop()  
                flag[mapp[c]] = False  
                a.append(c)  
                if c == t:  
                    break  
        else:  
            flag[mapp[t]] = True  
            card.append(t)  
            turn = False  
    else:  
        t = b.popleft()  
        if flag[mapp[t]]:  
            flag[mapp[t]] = False  
            b.append(t)  
            while True:  
                c = card.pop()  
                flag[mapp[c]] = False  
                b.append(c)  
                if c == t:  
                    break  
        else:  
            flag[mapp[t]] = True  
            card.append(t)  
            turn = True  
if a:  
    print(''.join(a))  
else:  
    print(''.join(b))
```

###### 109.分考场（dfs）
```python
def dfs(x, room, n):  
    global res  
    if room > n or room > res:  
        return  
    if x > n:  
        res = min(res, room)  
        return  
    for i in range(1, room + 1):  
        j = 1  
        while seat[i][j] and not rela[x][seat[i][j]]:  
            j += 1  
  
        if not seat[i][j]:  
            seat[i][j] = x  
            dfs(x + 1, room, n)  
            seat[i][j] = 0  
            break  
    if room + 1 > n:  
        return  
    seat[room + 1][1] = x  
    dfs(x + 1, room + 1, n)  
    seat[room + 1][1] = 0  
              
n = int(input())  
m = int(input())  
rela = [[False] * (n + 1) for _ in range(n + 1)]  
seat = [[0] * (n + 1) for _ in range(n + 1)]  
for _ in range(m):  
    a, b = map(int, input().split())  
    rela[a][b] = True  
    rela[b][a] = True  
res = n  
dfs(1, 1, n)  
print(res)
```

###### 110.合根植物（并查集）
```python
m, n = map(int, input().split())  
k = int(input())  
p = [i for i in range(m * n + 1)]  
  
  
def find(x):  
    if p[x] != x:  
        p[x] = find(p[x])  
    return p[x]  
  
  
for i in range(k):  
    a, b = map(int, input().split())  
    p[find(a)] = find(b)  
  
res = 0  
for i in range(1, n * m + 1):  
    if find(i) == i:  
        res += 1  
print(res)
```

###### 111.区间位移（二分）
```python
n = int(input())  
arr = [tuple(map(int, input().split())) for _ in range(n)]  
arr.sort(key=lambda a: a[1])  
  
  
def check(move, arr):  
    x = 0  
    while arr:  
        flag = False  
        for a in arr:  
            if a[0] - move <= x <= a[1] + move:  
                x = min(a[1] + move, x + a[1] - a[0])  
                if x >= 1e4:  
                    return True  
                arr.remove(a)  
                flag = True  
                break        if flag:  
            continue  
        else:  
            return False  
  
  
l = 0  
r = 1e4  
while r - l > 1e-3:  
    mid = (l + r) / 2  
    if check(mid, arr.copy()):  
        r = mid  
    else:  
        l = mid  
  
l = str(round(l, 1))  
i, f = l.split('.')  
if f is '0':  
    print(i)  
else:  
    print(l)
```

###### 126.交换瓶子（模拟）
```python
n=int(input())
arr=[0]+list(map(int,input().split()))
res=0
for i in range(1,n+1):
    if arr[i]!=i:
        j=arr.index(i)
        arr[i],arr[j]=i,arr[i]
        res+=1
print(res)
```

###### 11.移动距离（数学推导）
```python
w, m, n = map(int, input().split())  
  
  
# 0-n行  
# 1-n列  
def point(num):  
    global w  
    row = num // w  
    if row % 2 == 0:  
        col = num % w  
    else:  
        col = w - num % w + 1  
    return row, col  
  
  
r1, c1 = point(m)  
r2, c2 = point(n)  
print(abs(r1 - r2) + abs(c1 - c2))
```

###### 7.长草（bfs）
```python
from collections import deque
n,m=map(int,input().split())
p=[list(input()) for _ in range(n)]
k=int(input())
move=[(0,1),(0,-1),(-1,0),(1,0)]
q=deque()
for i in range(n):
    for j in range(m):
        if p[i][j] == 'g':
            q.append((i,j))
    
def bfs():
    global move,p,q,vist,m,n
    l=len(q)
    while l:
        x,y=q.popleft()
        for mv in move:
            tx,ty=x+mv[0],y+mv[1]
            if tx>=0 and tx<n and ty>=0 and ty<m and p[tx][ty]=='.' :
                p[tx][ty]='g'
                q.append((tx,ty))
        l-=1

for i in range(k):
    bfs()

for i in range(n):
    print(''.join(p[i]))

```

###### 136.奇怪的数列
```python
num = input()  
n = int(input())  
  
  
def count(s):  
    temp = ''  
    l = len(s)  
    i = 0  
    j = 0  
    while i < l:  
        if j < l and s[i] == s[j]:  
            j += 1  
        else:  
            temp += str(j - i) + s[i]  
            i = j  
    return temp  
  
  
for i in range(n):  
    num = count(num)  
  
print(num)
```

###### 5.穿越雷区（bfs）
```python
from collections import deque  
  
n = int(input())  
  
mp = [input().split() for _ in range(n)]  
move = [(1, 0), (-1, 0), (0, 1), (0, -1)]  
  
que = deque()  
vis = set()  
dis = {}  
parent = {}  
  
  
def start():  
    global que, mp  
    for i in range(n):  
        for j in range(n):  
            if mp[i][j] == 'A':  
                que.append((i, j))  
                dis[(i, j)] = 0  
                vis.add((i, j))  
                parent[(i, j)] = None  
                return  
  
def bfs():  
    global que, mp, vis, dis  
    while que:  
        x, y = que.popleft()  
        if mp[x][y] == 'B':  
            return dis[(x, y)]
        for i, j in move:
            xt = i + x
            yt = j + y
            if 0 <= xt < n and 0 <= yt < n and (xt, yt) not in vis and mp[x][y] != mp[xt][yt]:  
                vis.add((xt, yt))  
                que.append((xt, yt))  
                dis[(xt, yt)] = dis[(x, y)] + 1  
                parent[(xt, yt)] = (x, y)  
    return -1  
  
  
start()  
res = bfs()  
print(res)
```

###### 11.递增三元组（二分）
```python
n = int(input())  
a = sorted(map(int, input().split()))  
b = sorted(map(int, input().split()))  
c = sorted(map(int, input().split()))  
  
  
def lower(arr, tar):  
    global n  
    l = -1  
    r = n  
    while l + 1 != r:  
        mid = (l + r) // 2  
        if arr[mid] < tar:  
            l = mid  
        else:  
            r = mid  
    return l  
  
  
def upper(arr, tar):  
    global n  
    l = -1  
    r = n  
    while l + 1 != r:  
        mid = (l + r) // 2  
        if arr[mid] <= tar:  
            l = mid  
        else:  
            r = mid  
    return r  
  
  
res = 0  
for i in b:  
    x = lower(a, i) + 1  
    y = n - upper(c, i)  
    res += x * y  
print(res)
```

###### 18.日志统计(双指针)
```python
n,d,k=map(int,input().split())  
dic={}  
for _ in range(n):  
    ts,log=map(int,input().split())  
    dic[log]=dic.get(log,[])+[ts]  
  
def check(arr):  
    global d,k  
    print(arr)  
    length=len(arr)  
    for i in range(0,length):  
        count=0  
        for j in range(i,length):  
            if arr[j]<arr[i]+d:  
                count+=1  
                if count>=k:  
                    return True  
            else:  
                break  
    return False  
res=[]  
for log,arr in dic.items():  
    arr.sort()  
    if check(arr):  
        res.append(log)  
res.sort()  
for r in res:  
    print(r)
```

###### 12.完全二叉树的权值(完全二叉树)
```python
import math  
  
n = int(input())  
arr = list(map(int, input().split()))  
deep = int(math.log(n, 2)) + 1  
res = []  
for i in range(deep):  
    res.append(sum(arr[2 ** i - 1:2 ** i + 2 ** i - 1]))  
  
print(res.index(max(res)) + 1)
```

###### 1.外卖店优先级(模拟)
```python
n, m, t = map(int, input().split())  
mp = {}  
for _ in range(m):  
    ts, d = map(int, input().split())  
    mp[d] = mp.get(d, []) + [ts]  
  
  
def check(arr):  
    global t  
    tmp = arr[0]  
    count = 0  
    f = False  
    for a in arr:  
        if a - tmp > 1:  
            count -= a - tmp - 1  
            count = max(count, 0)  
        if count <= 3:  
            f = False  
        count += 2  
        if count > 5:  
            f = True  
        tmp = a  
    if count - (t - arr[-1]) <= 3:  
        return False  
    else:  
        return f  
  
  
res = 0  
for _, arr in mp.items():  
    arr.sort()  
    if check(arr):  
        res += 1  
print(res)
```

###### 14.大臣的旅费(树的直径)
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
d = deep[st]  
print((d + 21) * d // 2)
```

###### 16.剪格子(dfs)
```python
m, n = map(int, input().split())  
arr = [list(map(int, input().split())) for _ in range(n)]  
tar = 0  
for row in arr:  
    tar += sum(row)  
tar //= 2  
vis = [[False] * m for _ in range(n)]  
move = [(0, 1), (0, -1), (1, 0), (-1, 0)]  
res = float('inf')  
  
  
def dfs(x, y, cnt, plus):  
    global arr, vis, move, tar, res  
    if plus == tar:  
        res = min(res, cnt)  
        return  
    if plus > tar:  
        return  
    vis[x][y] = True  
    for mv in move:  
        tx = x + mv[0]  
        ty = y + mv[1]  
        if 0 <= tx < n and 0 <= ty < m and not vis[tx][ty]:  
            dfs(tx, ty, cnt + 1, plus + arr[tx][ty])  
    vis[x][y] = False  
  
  
dfs(0, 0, 1, arr[0][0])  
if res == float('inf'):  
    print(0)  
else:  
    print(res)
```