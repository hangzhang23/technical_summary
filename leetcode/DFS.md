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

#### LC22.括号生成
这里用dfs框架来做，如果按照[回溯](https://github.com/hangzhang23/technical_summary/blob/master/leetcode/%E5%9B%9E%E6%BA%AF%E6%B3%95.md)框架来做可以参考另一个md文件中的写法。
```python
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        res = []
        cur_str = ''

        def dfs(cur_str, left, right):
            """
            :param cur_str: 从根结点到叶子结点的路径字符串
            :param left: 左括号还可以使用的个数
            :param right: 右括号还可以使用的个数
            :return:
            """
            if left == 0 and right == 0:
                res.append(cur_str)
                return
            if right < left:
                return
            if left > 0:
                dfs(cur_str + '(', left - 1, right)
            if right > 0:
                dfs(cur_str + ')', left, right - 1)

        dfs(cur_str, n, n)
        return res
```

#### LC2.两数和
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        def dfs(l, r, i):
            if not l and not r and not i: return None
            s = (l.val if l else 0) + (r.val if r else 0) + i
            node = ListNode(s % 10)
            node.next = dfs(l.next if l else None, r.next if r else None, s // 10)
            return node
        return dfs(l1, l2, 0)
```
#### 剑指offer 35.复杂链表的复制

```python
"""
# Definition for a Node.
class Node:
    def __init__(self, x: int, next: 'Node' = None, random: 'Node' = None):
        self.val = int(x)
        self.next = next
        self.random = random
"""
class Solution:
    def copyRandomList(self, head: 'Node') -> 'Node':
        def dfs(head):
            if not head: return None
            if head in visited:
                return visited[head]
            # 创建新结点
            copy = Node(head.val, None, None)
            visited[head] = copy
            copy.next = dfs(head.next)
            copy.random = dfs(head.random)
            return copy
        visited = {}
        return dfs(head)
```
同样这道题也有[bfs做法](https://github.com/hangzhang23/technical_summary/master/leetcode/BFS.md).
