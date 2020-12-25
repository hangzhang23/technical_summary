# nSum问题求解框架
这个问题是leetcode里面比较经典的问题，二数之和，三数之和，四数之和。这里用排序+二分查找来为nSum问题统计一个框架。  

#### LC1.二数之和
首先要对数组进行排序，然后设立左右指针，当sum值和target相等时，返回对应的索引。但是要注意：
- 首先是排序时要用sorted把nums的排序结果放到一个tmp里面去，不要直接nums.sort()这样原数组就直接排序，返回的index是对不上的。
- 在sum和target相等时，用找到的index找到tmp中对应的value，然后用value返回原nums中找到原index，并注意找到左索引之后要弹出他，防止重复元素的干扰，然后找到右索引。
- 注意当右索引大于等于左索引得时候，右索引要加1.
**时间复杂度：O(NlogN)**  
```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        nums_tmp = sorted(nums)
        left, right = 0, len(nums_tmp) - 1
        while left < right:
            sum_2 = nums_tmp[left] + nums_tmp[right]
            if sum_2 == target:
                l = nums.index(nums_tmp[left])
                nums.pop(l)
                r = nums.index(nums_tmp[right])
                if r >= l:
                    r = r + 1
                return [l, r]
            elif sum_2 < target:
                left += 1
            elif sum_2 > target:
                right -= 1
        return []
```
当然也可以用哈希表的方法来做，只是这种方法不能作为nSum的框架。
- 查找j= target-i是否在列表里。利用这种思路，可以用值：index来建立哈希表，然后遍历nums时查看target-num的下标是否在字典里。在并且下标不相等，则可以返回下标。
```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        hashmap = {}
        for ind, num in enumerate(nums):
            hashmap[num] = ind

        for i, num in enumerate(nums):
            j = hashmap.get(target - num)
            if j is not None and i != j:
                return [i, j]
```
**附加:** 求和为sum的所有二元组合。
```python
def twoSum(nums, target):
    nums.sort()
    l, r = 0, len(nums) - 1
    res = []
    while l < r:
        sum_two = nums[l] + nums[r]
        left, right = nums[l], nums[r]
        if sum_two < target:
            while l < r and nums[l] == left:
                l += 1
        elif sum_two > target:
            while l < r and nums[r] == right:
                r -= 1
        else:
            res.append([left, right])
            while l < r and nums[l] == left: 
                l += 1
            while l < r and nums[r] == right:
                r -= 1
    return res
```

#### LC15.三数之和
如果将两数之和的方法搞明白，其实三数之和的方法就可以用两数之和来改进。  
在确定了第一个数之后，三数之和=num1+两数之和（target-num1）。则用穷举的方法来对nums中的每个数进行查询，并且跳过重复出现的数字。  
**时间复杂度：O(NlogN)**  
```python
class Solution:
    def twoSum(self, nums, start, target):
        l, r = start, len(nums) - 1
        res = []
        while l < r:
            sum_two = nums[l] + nums[r]
            left, right = nums[l], nums[r]
            if sum_two < target:
                while l < r and nums[l] == left:
                    l += 1
            elif sum_two > target:
                while l < r and nums[r] == right:
                    r -= 1
            else:
                res.append([left, right])
                while l < r and nums[l] == left: 
                    l += 1
                while l < r and nums[r] == right:
                    r -= 1
        return res
    
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        nums.sort()
        i, n = 0, len(nums)
        res = []
        while i < n:
            tup = self.twoSum(nums, i + 1, 0 - nums[i])
            for t in tup:
                t.append(nums[i])
                res.append(t)
            while i < n - 1 and nums[i] == nums[i + 1]:
                i += 1
            i += 1
        return res
        
```
