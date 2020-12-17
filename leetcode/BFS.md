# BFS框架总结
## 关键词：在图中找到七点start到终点target的最近距离。
BFS基本可以用的场景有，路径最近距离，替换单词的最少次数等等。  
关键知识点：要用到队列。  

#### LC111.二叉树最小深度
这道题是一道很经典的可以用BFS的题目，最小深度的话，就相当于顶点到节点的最小距离。
```python
def minDepth(root):
    from collections import deque
    if not root: return 0
    q = deque([root,1])
    while q:
        node, depth = q.popleft()
        if not node.left or node.right:
            return depth
        if node.left:
            q.append([root.left. depth+1])
        if node.right:
            q.append([root.right, depth+1])
    return 0
```
