# BFS框架总结
## 关键词：在图中找到七点start到终点target的最近距离。
BFS基本可以用的场景有，路径最近距离，替换单词的最少次数等等。  
关键知识点：要用到队列。  
框架：  
- 如果没有root，返回0，-1，false等特殊条件；
- 把原始root加入到队列中；
- 只要队列不为空，开始下列循环；
- （测量下q的长度，用于接下来的for循环）；
- 弹出队列的头部元素node；
- 判断是否到达终点，返回对应的要求。
- 将node的child加入队列‘
- 判断node的child是否已经走过（一般来说在二叉树中不需要这一步）。
- 增加步数

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
