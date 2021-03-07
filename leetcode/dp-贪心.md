# 贪心算法总结
贪心算法也算是dp的一种，相比于dp，贪心与要满足更多的条件，但是效率要比dp更高。如果一个算法问题使用暴力解法需要指数级时间，如果能使用动态规划消除重叠子问题，就可以降到多项式级别的时间，如果满足贪心选择性质，那么可以进一步降低时间复杂度，达到线性级别的。  
**贪心选择性质：每一步都做出一个局部最优的选择，最终的结果就是全局最优。**  
其题目描述和dp也很相近，求最值，求可能性。如果说dp在这里不适用的话，一般情况都可以用贪心来尝试。

### 区间调度
对于区间问题的处理，一般啦说第一步都是排序，相当于预处理降低后续操作难度。
#### LC435.无重叠区间
求最多有多杀哦区间不重叠的方法如下：  
1. 从区间集合intvs中选择一个区间x，这个区间是当前所有区间中结束最早的。
2. 把所有与x区间相交的区间从区间集合intvs中删除。
3. 重复1和2，直到intvs为空为止，之前选出的那些x就是最大不相交子集。

当求出最大不相交子集之后，用集合总数减去最大不相交子集就是至少需要去除的区间。
```python
class Solution:
    def eraseOverlapIntervals(self, intervals: List[List[int]]) -> int:
        if len(intervals) == 0: return 0
        intervals = sorted(intervals, key=lambda x: x[1])
        cnt = 1
        end = intervals[0][1]
        for i in range(1, len(intervals)):
            if intervals[i][0] < end:
                continue
            end = intervals[i][1]
            cnt += 1
        return len(intervals) - cnt
```
#### LC452.用最少的箭头射爆气球
这道题跟上面基本上是相同的，维度的条件在于边界在这里也算，其他的都和原来一样。
```python
class Solution:
    def findMinArrowShots(self, points: List[List[int]]) -> int:
        if len(points) == 0: return 0
        points = sorted(points, key= lambda x:x[1])
        cnt = 1
        end = points[0][1]
        for i in range(1, len(points)):
            if points[i][0] <= end:
                continue
            cnt += 1
            end = points[i][1]
        return cnt
```


#### LC55.跳跃游戏

```python
class Solution:
    def canJump(self, nums: List[int]) -> bool:
        n = len(nums)
        farthest = 0
        for i in range(n - 1):
            farthest = max(farthest, i + nums[i])
            if farthest <= i: return False
        return farthest >= n - 1
```

#### LC45.跳跃游戏II（hard）
leetcode中dp算法不能通过所有样例，但是贪心算法可以。贪心算法和dp的差别在于，并不是一直递归子问题，而是判断哪个选择更有潜力，就好比[3,1,4,2,1,5]这组数组，跳到nums[2]的可跳跃区域涵盖了索引[3:6]，比其他都大。在求最少条约次数的时候，肯定是最有选择。
```python
class Solution:
    def jump(self, nums: List[int]) -> int:
        n = len(nums)
        end, farthest = 0, 0
        jumps = 0
        for i in range(n - 1):
            farthest = max(nums[i]+i, farthest)
            if end == i:
                jumps += 1
                end = farthest
        return jumps
```

#### LC5698.构成特定和需要添加的最少元素
典型贪心法，先求数组和，然后用target减去sum得到残差的绝对值，这时候残差有三种情况：
- 如果残差=0，则不用添加元素
- 如果残差小于limit，也好说，添加一个小于limit的就行。
- 如果残差大于limit，用贪心策略来做，直接上limit（一个或者多个，也就是求余数）。
    - 假如能整除，则需要添加的个数就是商。
    - 不能整除，则等于商+1.
```python
class Solution:
    def minElements(self, nums: List[int], limit: int, goal: int) -> int:
        total_sum = sum(nums)
        delta = abs(total_sum - goal)
        if delta == 0: return 0
        elif delta <= limit: return 1
        else:
            if delta % limit == 0: return delta // limit
            else: return delta // limit + 1

```
