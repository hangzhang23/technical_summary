### 极大极小化
#### LC486.预言赢家
这道题用递归是会超时的，所以用动态规划，动态规划中规定dp[i][j]是从数组中第i位到第j位的当前玩家与另一玩家的得分差的最大值（这是一个比较特别的点）。而其中这个二维dp数组中，长宽是一样的，而正对角线中表示的是从i到i位的得分差最大值。
首先确定对角线上的值，这个是等于nums对应位的值的，因为就剩一位了。
然后就是dp转移方程：
dp[i][j] = max(nums[i] - dp[i+1][j], nums[j]- dp[i][j-1])
对上面的解释是，当面对[i,j]范围的数时，如果我选择i，则对手选择范围是[i+1,j]，相反如果我选择j，则对手选择范围是是[i,j-1]。所以对应的这一步新的得分差是nums[i]-dp[i+1,j]和nums[j]-dp[i,j-1]，并从他们中取最大值。

最后的结果是dp[0][length-1]是否大于等于0，因为其实定义dp就是i到j的得分差最大值，并且是对应先手玩家。


```python
class Solution:
    def PredictTheWinner(self, nums: List[int]) -> bool:
        length = len(nums)
        if length == 1: return True

        dp = [[0]*length for i in range(length)]
        for i in range(length):
            dp[i][i] = nums[i]

        for i in range(length - 2, -1, -1):
            for j in range(i + 1, length):
                dp[i][j] = max(nums[i] - dp[i+1][j], nums[j]- dp[i][j-1])
        
        return dp[0][length - 1] >= 0
```

#### LC877.石子游戏
跟上面486的做法一样

#### LC1690.石子游戏VII
这道题和877，还有486的做法是相近的，但是不一样的点在于：  
1. 这里是求剩余数字和，所以要先求前缀和，对stones累加；
2. dp方程也有所不同，虽然还是表示剩余石头从i到j的当前玩家和另一玩家得分差的最大值，则A的得分是本次删掉石头后的得分-对手下次比我多的得分较大方案者。
```python
class Solution:
    def stoneGameVII(self, stones: List[int]) -> int:
        N = len(stones)   
        for i in range(1, N):
            stones[i] += stones[i - 1]

        dp = [[0]*N for _ in range(N)]
        for i in range(N - 2, -1, -1):
            for j in range(i + 1, N):
                dp[i][j] = max(stones[j] - stones[i] - dp[i+1][j], stones[j - 1] - (stones[i - 1] if i > 0 else 0) - dp[i][j - 1])
        return dp[0][N - 1]
```
