# 1. 回溯
- 组合问题：N个数里面按一定规则找出k个数的集合
- 切割问题：一个字符串按一定规则有几种切割方式
- 子集问题：一个N个数的集合里有多少符合条件的子集
- 排列问题：N个数按一定规则全排列，有几种排列方式
- 棋盘问题：N皇后，解数独等等
## 1.1. 组合
### 1.1.1. 模板
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

### 1.1.2. skill
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
## 1.2. 排列
- 使用 `visit` 数组标记已经取过的数


# 2. keys
- 函数尽量使用按引用传递 `&`


# 3. STL
- 提取 `<str>` 的子字符串：`<str>.substr(pos, len)`
	- `pos`：提取的起始下标
	- `len`：提取的长度
- 重置 `vector` 大小：`<vector>.resize(num, in)`
	- `num`：填充 `in` 的个数
	- `in`：填充的内容（eg. `vector<bool>(s.size(), false)`）
- set / map
	```cpp
	// 哈希表
	unordered_map<K, V>
	unordered_set<K>
	
	<set>.count(<item>)  // 判断元素是否存在，返回 0 / 1
	<set>.emplace(<item>) //插入元素，或者用 insert 方法
	
	====
	// 红黑树，元素有序排列
	map<K, V>
	```

