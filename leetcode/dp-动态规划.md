# 动态规划总结
leetcode中比较大规模的在面试中经常出现的一类题就是动态规划，这个类型的题目也算是LC中普遍比较难的题目。难在一如何**写出状态定义**，二**状态转移方程**是否合理。  

**关键词：计数，求最大值，最小值，求存在性。** 

DP问题包含重叠子问题和**最优子结构**，重叠子问题是子问题会多次出现，因此需要DP表把之前的结果记录下来；最优子结构就算现在和之前状态存在递推关系，且子问题之间都是相互独立的，就是dp[i]=dp[i-1]+1这样的转移方程。

步骤：
1. 确定状态：1）研究最优策略的最后一步，2）化为子问题（自底向上）。
2. 状态转移方程：根据子问题定义直接得到。
3. 初始条件和边界条件界定。
4. 计算顺序。

时间复杂度：子问题总数$\times$解决每个子问题的时间

#### LC70.爬楼梯
入门DP的题目，归属于计数问题。爬楼梯的最后一步是，在登上最后一级台阶时，可以从n-1或者n-2级上来，同样在n-1级上也对应可以从n-2和n-3级上来，则由此发现可以用递归来更新状态转移方程。所以我们的状态定义是共有多少种方法登上第i层。
而其对应的状态转移方程也就是第i层的方法等于第i-1和第i-2的方法的和。
```python
dp[i] = dp[i-1] + dp[i-2]
```
所以我们首先可用的通用方法为：
```python
class Solution:
    def climbStairs(self, n: int) -> int:
        if n <= 2: return n
        dp = [0]*n
        dp[0] = 1
        dp[1] = 2
        for i in range(2, n):
            dp[i] = dp[i-1] + dp[i-2]
        return dp[n-1]
```
上述方法时间复杂度和空间复杂度分别为O(n)和O(n)，并发现其实每次计算也只用到了两个变量，所以可以用滚动数组的方法，将空间复杂度进一步降低到O(1)。
```python
class Solution:
    def climbStairs(self, n: int) -> int:
        if n==1 or n==2: return n
        a, b, temp = 1, 2, 0
        for i in range(3,n+1):
            temp = a + b
            a = b
            b = temp
        return temp
```

#### LC509.斐波那契数列
这道题跟上面的70爬楼梯是一道题，题干已经直接把状态转移方程给出来了。所以这里用的方法是一样的。
```python
class Solution:
    def fib(self, n: int) -> int:
        if n == 0: return 0
        elif n == 1: return 1
        else:
            f0, f1  = 0, 1
            for i in range(2, n + 1):
                tmp = f1 + f0
                f0 = f1
                f1 = tmp
        return tmp
```

#### LC198.打家劫舍
这道题也是很经典的dp套路问题，题目属于**求最值**。
状态定义为：在偷到最后一家时的最大收益。  
状态转移方程：dp[i] = max(dp[i - 1], dp[i - 2] + nums[i])，对此解释为当偷第i家时，只能偷i-2家，此时的收益与与偷i-1家的收益取最大值作为在偷到第i家的最大收益。
```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if len(nums) == 0: return 0
        N = len(nums)
        dp = [0]*(N+1)
        dp[1] = nums[0]
        for i in range(2, N+1):
            dp[i] = max(dp[i - 1], nums[i - 1] + dp[i - 2])
        return dp[-1]
```
用来降低空间复杂度的方法O(1):
```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        pre2 = 0
        pre1 = 0
        for i in range(0, len(nums)):
            cur = max(pre2 + nums[i], pre1)
            pre2 = pre1
            pre1 = cur

        return pre1
```

#### LC213.打家劫舍II
这道题跟198不同的点在于将列表变成了环形链表，所以在抢劫的时候，要么两家都不抢，或者抢第一家，就不能抢最后一家，或者反过来。
而上面的情况肯定是要从后两者中选，所以对上述的方法做修改，在不抢第一家和不抢最后一家中选择最大值，所以我在这里用一个函数来计算nums的结果，给nums两个不同的数组，最后取较大值。
```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if len(nums) == 1: return nums[0] 
        def my_rob(nums):
            cur, pre = 0, 0
            for num in nums:
                cur, pre = max(pre+num, cur), cur
            return cur
        return max(my_rob(nums[:-1]), my_rob(nums[1:]))
```

#### LC337.打家劫舍III
这道题将数组变成了二叉树, 但是做题步骤还是一样。这里我们设置两个值v1和v2分别表示抢和不抢的收益。则每次v1都等于左子树不抢和右子树不抢加上这个点的值（即下家不能抢），而v2表示的是左子树和右子树的最大值之和（即下家抢不抢要取决于情况）。
```python
class Solution:
    def rob(self, root: TreeNode) -> int:
        def rob_iter(root):
            if not root: return 0, 0
            left = rob_iter(root.left)
            right = rob_iter(root.right)

            v1 = left[1] + root.val + right[1]
            v2 = max(left) + max(right)
            return v1, v2
        return max(rob_iter(root))
```

#### LC322.零钱兑换
零钱兑换是一个**求最值**的典型dp问题，他的子问题是：n-1个硬币的最少硬币数。假如有1，2，5三种硬币，目标金额是11，则拼出11元的最少硬币数的子问题就是分别拼出10，9，6元的最少硬币数再+1，很明显这三个字问题是相互独立的。  
状态定义：目标为target时的最少硬币数。  
状态转移方程：
$$
dp\left ( n \right )\left\{\begin{matrix}
0 &,n=0 \\ 
-1&,n<0 \\
min\left \{ dp(n-coin)+1, coin\in coins \right \}&,n>0
\end{matrix}\right.
$$
```python
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        def dp(n):
            if n == 0: return 0
            if n < 0: return -1
            res = float('inf')
            for coin in coins:
                if dp(n - coin) == -1: continue
                res = min(res, 1 + dp(n - coin))
            return res if res != float('inf') else -1
        return dp(amount)
```

如果按照迭代自底向上使用dp表来消除重叠子问题，则  
状态定义：当目标金额为i时的最少硬币数。  
状态转移方程：dp[i] = min(dp[i], dp[i - coin])
```python
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        dp = [amount + 1] * (amount + 1)
        dp[0] = 0
        for i in range(amount + 1):
            for coin in coins:
                if i - coin < 0: continue
                dp[i] = min(dp[i], 1 + dp[i - coin])
        return dp[amount] if dp[amount] != amount + 1 else -1 
```

#### LC300.最长递增子序列
最长递增子序列也是一道典型dp题目。  
状态定义：**dp[i]表示nums[i]结尾时最长递增子序列的长度。**  
base case：dp[i] = 1, 因为每个字母结尾最短也是1。  
用数学归纳思想来推导状态转移方程：找到最后一个数字的最长子序列，肯定是倒数第二个数字中截尾比nums[-1]小的子序列，然后把nums[-1]接到最后，形成一个新的递增子序列长度并+1，而且比nums[-1]小的子序列中最长的那个...由此类推，我们可以用一个循环来更新dp数组。
```python
for j in len(i):
    if nums[j] < nums[i]：
        dp[i] = max(dp[i], dp[j] + 1)
```
所以最终的代码是：
```python
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        if not nums: return 0
        dp = [1] * len(nums)
        for i in range(len(nums)):
            for j in range(i):
                if nums[i] > nums[j]:
                    dp[i] = max(dp[i], dp[j]+1)
        return max(dp)
```

#### LC354.俄罗斯套娃信封问题（hard）
这道题算是最长递增子序列的一个变种问题，但是由于合法的嵌套必须要长宽都大于才行，而LIS只能在数组里找。
所以在做LIS算法之前，需要做一个排序。而这里排序的tricks就是先对w进行升序排序，如果出现相同的情况，则对h做降序排序。
然后对h组成的数组做LIS就是最后的答案。  
算法的理解重点：对于w相同的数对，h做降序排列时，才能保证只会选择一个计入LIS。
```python
class Solution:
    def maxEnvelopes(self, envelopes: List[List[int]]) -> int:
        if not envelopes:
            return 0
        N = len(envelopes)
        envelopes.sort(key=lambda x: (x[0], -x[1]))
        res = 0
        dp = [1] * N
        for i in range(N):
            for j in range(i):
                if envelopes[j][1] < envelopes[i][1]:
                    dp[i] = max(dp[i], dp[j] + 1)
        return max(dp)
```


#### LC53.最大子数组和
子数组和通常会考虑到滑动窗口，但是这道题里有负数，所以不能用滑动窗口，因为移动左右指针的判断条件不明确，所以这里用dp来做。  
状态定义：**dp[i]表示以nums[i]结尾的最大子数组和**，则这种情况的最优值是遍历整个dp数组取最大值。 
状态转移方程：这个就很巧妙了，在dp[i-1]得到之后，dp[i]要从nums[i]和dp[i-1]+nums[i]中取最大值。  
```python
dp[i] = max(nums[i], dp[i - 1] + nums[i])
```
所以最终的代码是：
```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        if not nums or len(nums) == 0: return 0
        preSum = nums[0]
        maxSum = preSum
        for i in range(1, len(nums)):
            if preSum > 0:
                preSum = preSum + nums[i]
            else:
                preSum = nums[i]
            maxSum = max(maxSum, preSum)
        return maxSum
```

#### LC1143.最长公共子序列
两个字符串求子序列问题，都是两个指针i和j分别在两个字符串上移动，大概率是dp思路。  
**自顶向下**：  
自顶向下问题都是用递归或者迭代函数，在这里我们设计一个dp函数用来计算s1[i:]和s2[j:]的最长公共子序列。  
设置一个备忘录，用来消除重叠子问题。而剩下函数考虑的情况：
1. 首先是i和j都等于最长,则表示为空，返回0；
2. 当s1[i] == s2[j]，则表示存在公共子序列中，所以要同时查i和j的下一位，并且总长要+1；
3. 当s1[i] != s2[j]，可能s1[i]不在，或者s2[j]不在，或者都不在，但是我们只要选出最长的值就可以了。
4. 其中dp中memo用来记录结果，如果已经算过了，就直接返回。

```python
class Solution:
    def longestCommonSubsequence(self, text1: str, text2: str) -> int:
        m, n = len(text1), len(text2)
        self.memo = [[-1 for _ in range(n + 1)] for _ in range(m + 1)]
        return self.dp(text1, 0, text2, 0)
    
    def dp(self, s1, i, s2, j):
        if i == len(s1) or j == len(s2): return 0
        if self.memo[i][j] != -1: return self.memo[i][j]
        if s1[i] == s2[j]:
            self.memo[i][j] = 1 + self.dp(s1, i + 1, s2, j + 1)
        else:
            self.memo[i][j] = max(self.dp(s1, i + 1, s2, j), self.dp(s1, i, s2, j + 1))
        return self.memo[i][j]
```
**自底向上**：  
自底向上刚好相反，则是从1计算到最后，但是思路跟上面几乎是差不多的。
```python
class Solution:
    def longestCommonSubsequence(self, text1: str, text2: str) -> int:
        m, n = len(text1), len(text2)
        ans = 0
        dp = [[0 for _ in range(n + 1)] for _ in range(m + 1)]
        for i in range(1, m + 1):
            for j in range(1, n + 1):
                if text1[i - 1] == text2[j - 1]:
                    dp[i][j] = dp[i - 1][j - 1] + 1
                    ans = max(ans, dp[i][j])
                else:
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])
        return ans
```

#### LC583.两个字符串的删除操作
这不就最长公共子序列的长度分别用word1和word2的长度减一下？直接套用上面的代码就能做
```python
class Solution:
    def minDistance(self, word1: str, word2: str) -> int:
        m, n = len(word1), len(word2)
        self.memo = [[-1 for _ in range(n + 1)] for _ in range(m + 1)]
        lcs = self.dp(word1, 0, word2, 0)
        return m - lcs + n - lcs
    
    def dp(self, s1, i, s2, j):
        if i == len(s1) or j == len(s2): return 0
        if self.memo[i][j] != -1: return self.memo[i][j]
        if s1[i] == s2[j]:
            self.memo[i][j] = 1 + self.dp(s1, i + 1, s2, j + 1)
        else:
            self.memo[i][j] = max(self.dp(s1, i + 1, s2, j), self.dp(s1, i, s2, j + 1))
        return self.memo[i][j]
```

#### LC712.两个字符串的最小ASCII码删除和
对583的一点变形，修改一下base case和状态转移部分就可以写出下列代码。这道题跟lcs在base case上的差别是，计算lcs长度，如果一个字符串为空，那么lcs长度必然是0；这道题如果一个字符串为空，另一个字符串必然要被全部删除，所以要计算另一个字符串所有字符的ascii码之和。
```python
class Solution:
    def minimumDeleteSum(self, s1: str, s2: str) -> int:
        m, n = len(s1), len(s2)
        self.memo = [[-1 for _ in range(n + 1)] for _ in range(m + 1)] 
        return self.dp(s1, 0, s2, 0)

    def dp(self, s1, i, s2, j):
        res = 0
        if i == len(s1):
            while j < len(s2):
                res += ord(s2[j])
                j += 1
            return res
        if j == len(s2):
            while i < len(s1):
                res += ord(s1[i])
                i += 1
            return res
        if self.memo[i][j] != -1: return self.memo[i][j]
        if s1[i] == s2[j]: self.memo[i][j] = self.dp(s1, i+1, s2, j+1)
        else:
            self.memo[i][j] = min(ord(s1[i]) + self.dp(s1, i+1, s2, j), ord(s2[j]) + self.dp(s1, i, s2, j+1))

        return self.memo[i][j]
```

#### LC238.除自身以外数组的乘积
这道题可以用一个非严格意义的dp方法来做，动态定义就是第i位的非自身元素的乘积。  
状态转移方程：dp[i] = dp[i-1] × nums[i-1] × 右边乘积。  
所以这个状态转移方程要分两次来做，一次循环为求左乘积放在dp数组里，然后第二次循环从尾部计算右乘积，再分别更新dp数组。  
时间复杂度：O(n)  
空间复杂度：O(1)
```python
class Solution:
    def productExceptSelf(self, nums: List[int]) -> List[int]:
        n = len(nums)
        res = [1]*(n)
        for i in range(1, n):
            res[i] = res[i - 1] * nums[i - 1]

        right = 1
        for j in range(n - 2, -1, -1):
            right *= nums[j + 1]
            res[j] *= right

        return res
```

#### LC152.乘积最大子数组
dp算这个题目的其中一种做法，这里状态定义为：在当前位置的乘积最大的连续子数组的乘积res。  
状态转移方程考虑比较多，首先要计算一下目前的最大值，而这个最大值要从pre_max × 当前数，pre_min × 当前数，以及当前数三个里面选出最大（因为考虑到有负数出现），之后更新最小值，同样的比较对象组。之后更新res，为之前的res与cur_max的最大值，然后更新pre_max和pre_min.
```python
class Solution:
    def maxProduct(self, nums: List[int]) -> int:
        if not nums: return 
        res = nums[0]
        pre_max = nums[0]
        pre_min = nums[0]
        for num in nums[1:]:
            cur_max = max(pre_max * num, pre_min * num, num)
            cur_min = min(pre_max * num, pre_min * num, num)
            res = max(res, cur_max)
            pre_max = cur_max
            pre_min = cur_min
        return res
```

#### LC221.最大正方形
状态定义为：在当前点i,j为右下角，能向左上围成的最大正方形的边长。并仅当该位置为1时，才有可能存在正方形。  
状态转移方程：dp[i][j] = min(dp[i-1][j-1], dp[i][j-1], dp[i-1][j])+1。  
含义为若当前位置为11，则此处可以构成的最大正方形的边长，是其正上方，左侧，和左上界三者共同约束的，且为三者中的最小值加1。  
O(mn)/O(mn)  
```python
class Solution:
    def maximalSquare(self, matrix: List[List[str]]) -> int:
        if not matrix: return 0
        m, n = len(matrix), len(matrix[0])
        res = 0
        dp = [[0]*(n+1) for _ in range(m+1)]
        for i in range(1, m + 1):
            for j in range(1, n + 1):
                if matrix[i-1][j-1] == '1':
                    dp[i][j] = min(dp[i-1][j-1], dp[i][j-1], dp[i-1][j])+1
                    res = max(res, dp[i][j])
        return res*res
```
空间优化：当前位置的最大边长仅取决于上一行和左侧最大边长，因此可将空间优化到O(n)O(n)。  
借助pre来保存当前位置的左侧。pre的更新：
- 同一行时，若当前位置为00，需要将prepre置为0。
- 换行时，需要置为0
```python
class Solution:
    def maximalSquare(self, matrix: List[List[str]]) -> int:
        if not matrix: return 0
        m, n = len(matrix), len(matrix[0])
        res, pre = 0, 0
        dp = [0]*(n+1)
        for i in range(0, m):
            for j in range(1, n + 1):
                tmp = dp[j]
                if matrix[i][j-1] == '1':
                    dp[j] = min(pre, dp[j-1], dp[j]) + 1
                    res = max(res, dp[j])
                else:
                    dp[j] = 0
                pre = tmp
            pre = 0
        return res*res
```

#### LC264.丑数II（剑指offer 49.丑数）
没考虑到这道题是一个dp问题，
```python
class Solution:
    def nthUglyNumber(self, n: int) -> int:
        dp, a, b, c = [1] * n, 0, 0, 0
        for i in range(1, n):
            n2, n3, n5 = dp[a] * 2, dp[b] * 3, dp[c] * 5
            dp[i] = min(n2, n3, n5)
            if dp[i] == n2: a += 1
            if dp[i] == n3: b += 1
            if dp[i] == n5: c += 1
        return dp[-1]
```

#### 剑指offer 46.把数字翻译成字符串
算是比较典型的可以用dp解的题目，这道题对于构成状态转移方程dp[i]的限制条件就一个，当前位和上一位两个数构成的数字能否翻译。
- 能翻译，则dp[i] = dp[i - 1] + dp[i - 2]，表示由i-1和i-2构成的方法总和（上一步的所有方法+本位数以及上两步的所有方法+本位数）
- 不能翻译，则dp[i] = dp[i - 1]，即跟上一位的翻译方法数一样的（上一步的所有方法+本位数）
时间复杂度：O(n)  
空间复杂度：O(n)  
```python
class Solution:
    def translateNum(self, num: int) -> int:
        if 0 <= num <= 9: return 1
        nums = [int(i) for i in str(num)]
        dp = [0]*len(nums)
        dp[0] = 1
        if 10 <= 10*nums[0] + nums[1] <= 25:
            dp[1] = 2
        else:
            dp[1] = 1
        
        for i in range(2, len(nums)):
            if 10 <= 10*nums[i - 1] + nums[i] <= 25:
                dp[i] = dp[i - 1] + dp[i - 2]
            else:
                dp[i] = dp[i - 1]

        return dp[-1]
```
当然观察一下发现状态转移方程的更新也只需要两个变量，所以可以将空间复杂度降低为O(1)
```python
class Solution:
    def translateNum(self, num: int) -> int:
        if 0 <= num <= 9: return 1
        nums = [int(i) for i in str(num)]
        dp_0 = 1
        if 10 <= 10*nums[0] + nums[1] <= 25:
            dp_1 = 2
        else:
            dp_1 = 1
        for i in range(2, len(nums)):
            tmp, dp_0 = dp_0, dp_1
            if 10 <= 10*nums[i - 1] + nums[i] <= 25:
                dp_1 = dp_0 + tmp
        return dp_1
```
由于状态转移方程比较有规律，也可以用[递归的方法](https://github.com/hangzhang23/technical_summary/blob/master/leetcode/%E9%80%92%E5%BD%92.md)

#### 剑指offer 60.n个骰子的点数
```python
class Solution:
    def dicesProbability(self, n: int) -> List[float]:
        dp = [ [0 for _ in range(6*n+1)] for _ in range(n+1)]
        for i in range(1,7):
            dp[1][i] = 1

        for i in range(2,n+1):
            for j in range(i,i*6+1):
                for k in range(1,7):
                    if j >= k+1:
                        dp[i][j] +=dp[i-1][j-k]
        res = []
        for i in range(n,n*6+1):
            res.append(dp[n][i]*1.0/6**n)
        return res
```
