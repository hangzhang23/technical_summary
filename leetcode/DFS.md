# DFS框架总结
## 关键词：深度，从起点开始到终点的可达性问题。
DFS多应用于树/图的遍历、嵌套关系处理、回溯等，可以用递归、堆栈(stack)实现DFS过程。  
其实很多树和图问题能用bfs来求的话也可以用dfs写，他们在时间复杂度上是一样的，但是由于是一条路走到黑，所以很容易超时。但是dfs的空间复杂度一般较低。  
DFS会用到递归和stack。  
基本框架：
- 定义dfs函数；
- 判断越界条件，重点条件；
- （特殊状态，剪枝）；
- for循环扩展范围；
- if状态合法：
    - 修改操作，标记；
    - dfs递归
    - （还原标记（回溯））

#### 剑指offer 55-I.二叉树深度
非常典型的一个用dfs可以写很简短的一道题，递归就完成了。  
判断是否还是节点，然后返回递归中的较大一个。
```python
class Solution:
    def maxDepth(self, root: TreeNode) -> int:
        if not root: return 0
        return max(self.maxDepth(root.left), self.maxDepth(root.right)) + 1
```
#### 剑指offer 55-II.平衡二叉树
平衡二叉树的定义是任意节点的左右子节点深度不大于1。  
这里的第一想法还是像上一题一样，先建立一个depth来求每个节点的深度，然后用递归去判断每个节点的左右子树情况。

```python
class Solution:
    def isBalanced(self, root: TreeNode) -> bool:
        if not root: return True
        if (abs(self.height(root.left) - self.height(root.right)) > 1):
            return False
        else:
            return self.isBalanced(root.left) and self.isBalanced(root.right)
    def height(self,root):
        if not root: return 0
        return 1+max(self.height(root.left),self.height(root.right))
```
#### LC98.验证二叉搜索树
搜索树的特点是
- 节点的左子树只包含小于当前节点的数。
- 节点的右子树只包含大于当前节点的数。
- 所有左子树和右子树自身必须也是二叉搜索树。  
则在这里用用一个递归来做搜索。
```python
class Solution:
    def isValidBST(self, root: TreeNode) -> bool:
        """
        递归法，用二叉搜索树的性质，根节点的左子树的值都要小于根节点，而右子树要大于根节点。则递归函数设计为一个有值域的判断函数，当超过这个值域则不是二叉搜索树。
        而在root的左子树中要符合上界为root.val，而root右子树的下界为root.val.
        """
        def helper(node, lower = float('-inf'), upper = float('inf')):
            if not node:
                return True
            
            val = node.val
            if val <= lower or val >= upper:
                return False
            if not helper(node.right, val, upper):
                return False
            if not helper(node.left, lower, val):
                return False
            return True
        
        return helper(root)
```

#### LC773.图像渲染
这道题用dfs可做。
```python
class Solution:
    def floodFill(self, image: List[List[int]], sr: int, sc: int, newColor: int) -> List[List[int]]:
        oldColor = image[sr][sc]
        rows, cols = len(image), len(image[0])

        def dfs(row, col):
            if 0 <= row < rows and 0 <= col < cols and image[row][col] == oldColor:
                if image[row][col] == -1: return

                image[row][col] = -1

                dfs(row - 1, col)
                dfs(row + 1, col)
                dfs(row, col - 1)
                dfs(row, col + 1)

        dfs(sr, sc)

        for row in range(rows):
            for col in range(cols):
                if image[row][col] == -1:
                    image[row][col] = newColor

        return image
```
