# Algorithm

## 1.回溯

- 组合问题：N个数里面按一定规则找出k个数的集合
- 切割问题：一个字符串按一定规则有几种切割方式
- 子集问题：一个N个数的集合里有多少符合条件的子集
- 排列问题：N个数按一定规则全排列，有几种排列方式
- 棋盘问题：N皇后，解数独等等

### 1.1组合

- 递归函数的返回值以及参数

  定义两个集合：

  - 用来存放符合条件的单一结果`path`
  - 用来存放符合条件的结果集合`result`

  参数`startIndex`：用来记录本层递归中集合从哪开始遍历

- 回溯函数终止条件

  `path`如果达到`k`，则用`result`保存`path`，并终止递归

- 单层搜索过程

  `for循环`横向遍历递归树，`递归`纵向遍历递归树

模板：

```java
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

剪枝：若剩余元素不足目标个数，直接返回

LeetCode77

````java
class Solution {
    public LinkedList<Integer> path = new LinkedList<>();
    public List<List<Integer>> result = new ArrayList<>();

    public List<List<Integer>> combine(int n, int k) {
        backtracking(1, n, k);
        return result;
    }

    private void backtracking(int startIndex, int n, int k) {

        if (path.size() == k) {
            // 需要添加新对象，不能直接添加path
            result.add(new ArrayList<>(path));
            return;
        }

        for (int i = startIndex; i <= n; i++) {
            // 如果剩余元素不够填充，则直接返回
            if (n - i + 1 < k - path.size()) {
                return;
            }
            path.add(i);
            backtracking(i + 1, n, k);
            // 用此方式来移除最后一个元素
            if (!path.isEmpty()) {
                path.remove(path.size() - 1);
            }

        }
    }
}
````

