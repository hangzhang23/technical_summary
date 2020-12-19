# DFS框架总结
## 关键词：深度，从起点开始到终点的可达性问题。  
其实很多树和图问题能用bfs来求的话也可以用dfs写，他们在时间复杂度上是一样的，但是由于是一条路走到黑，所以很容易超时。但是dfs的空间复杂度一般较低。  

#### 剑指offer 55-I.二叉树深度
非常典型的一个用dfs可以写很简短的一道题，递归就完成了。
```python
class Solution:
    def maxDepth(self, root: TreeNode) -> int:
        if not root: return 0
        return max(self.maxDepth(root.left), self.maxDepth(root.right)) + 1
```
