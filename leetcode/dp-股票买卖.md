# 股票买卖题目框架
股票买卖的框架其实非常清晰，从目前的题目看都可以用动态规划表来做。  

根据labuladong的总股票框架来说：如下三维数组框架适用所有的股票问题：
- i为第几天，k为到第i天的交易次数，s为第i天是否持有。
```python
dp[i][j][0 or 1]
# 0 <= i <= n - 1, 1<=k<=K, n为天数，K为最多交易次数

for i in range(n):
    for k in range(1, K):
        for s in {0, 1}:
            # dp[i][k][s] = max(buy, sell, rest)
            dp[i][k][s] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i]) # (rest或者sell)
            dp[i][k][s] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i]) # (rest或者buy)

# base case：
dp[-1][k][0] = dp[i][0][0] = 0
dp[-1][k][1] = dp[i][0][1] = -infinity
```

#### LC121.单次交易的买卖股票最佳时机
这道easy级别的题目，入手点就是dp，则首先要考虑的就是dp方程要如何写。  
dp方程是在每一天对比今天卖出的利益和昨天的最大利益作比较，取较大值作为新的最大收益。并且在遍历每天的时候要及时更新最低价格作为买入价。
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices: return 0
        max_profit = 0
        min_price = prices[0]
        for i in range(len(prices)):
            if min_price > prices[i]:
                min_price = prices[i]
            max_profit = max(max_profit, prices[i] - min_price)
        return max_profit
```
用框架写如下，规定好dp的基本条件，因为这里k不起作用所以缩减为两维空间。
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if len(prices) == 0: return 0
        n = len(prices)
        dp = [[0 for _ in range(2)] for _ in range(n)]
        for i in range(n):
            if i - 1 == -1:
                dp[i][0] = 0
                dp[i][1] = -prices[i]
                continue
            dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] + prices[i])
            dp[i][1] = max(dp[i - 1][1], -prices[i])
        return dp[n - 1][0]
```

#### LC122.多次交易的买卖股票最佳时机
非框架型写法。多次交易的话，策略为每日交易，上涨交易日进行买卖，而下跌交易日不买买。
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        profit = 0
        for i in range(1, len(prices)):
            tmp = prices[i] - prices[i - 1]
            if tmp > 0: profit += tmp
        return profit
```
第二种写法，根据框架来做，这里原本的dp方程是：
> ```python
> dp[i][k][0] = max(dp[i - 1][k][0], dp[i - 1][k][1] + prices[i])
> dp[i][k][1] = max(dp[i - 1][k][1], dp[i - 1][k - 1][0] - prices[i])
> ```
> 由于k和k-1在这里是一样的。则可以改写框架为：
> ```python
> dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] + prices[i])
> dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] - prices[i])
> ```
所以第一个改进的框架方法是：
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if len(prices) == 0: return 0
        n = len(prices)
        dp = [[0 for _ in range(2)] for _ in range(n+1)]
        dp[-1][0] = 0
        dp[-1][1] = float('-inf')
        for i in range(n):
            dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] + prices[i])
            dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] - prices[i])
        return dp[n - 1][0]
```
这里对于dp方程观察发现每个dp[i][0]和dp[i][1]其实是迭代计算的，所以这里用两个变量缩减使用空间，则变成：
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if len(prices) == 0: return 0
        n = len(prices)
        dp_i_0, dp_i_1 = 0, float('-inf')
        for i in range(n):
            tmp = dp_i_0
            dp_i_0 = max(dp_i_0, dp_i_1 + prices[i])
            dp_i_1 = max(dp_i_1, tmp - prices[i])
        return dp_i_0
```

#### LC309.股票交易带冷冻期
冷冻期要求在卖出的第二天不能交易，则在dp[i][1]的状态方程中的max的第二项换成dp[i-2]
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        n = len(prices)
        if n == 0: return 0
        dp = [[0 for _ in range(2)] for _ in range(n+2)]
        dp[-1][1] = float('-inf')
        for i in range(n):
            dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
            dp[i][1] = max(dp[i-1][1], dp[i-2][0] - prices[i]) 
        return dp[n - 1][0]
```
同样可以减少为两个变量迭代计算。
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        n = len(prices)
        if n == 0: return 0
        dp_i_0, dp_i_1, dp_i_0_pre = 0, float('-inf'), 0
        for i in range(n):
            tmp = dp_i_0
            dp_i_0 = max(dp_i_0, dp_i_1 + prices[i])
            dp_i_1 = max(dp_i_1, dp_i_0_pre - prices[i])
            dp_i_0_pre = tmp
        return dp_i_0
```
#### LC714.交易股票带手续费
其实跟122的方法几乎相同，只要在计算dp[i][0]得时候，对max()的第二项减去手续费就行了。另外在第二个dp方程中因为不牵扯卖出所以不用考虑手续费。
```python
class Solution:
    def maxProfit(self, prices: List[int], fee: int) -> int:
        n = len(prices)
        if n == 0: return 0
        dp = [[0 for _ in range(2)] for _ in range(n + 1)]
        dp[-1][1] = float('-inf')
        for i in range(n):
            dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i] - fee)
            dp[i][1] = max(dp[i-1][1], dp[i-1][0] - prices[i])
        return dp[n - 1][0]
```
同样跟122一样可以用两个变量迭代，则可以变成下列代码。
```python
class Solution:
    def maxProfit(self, prices: List[int], fee: int) -> int:
        n = len(prices)
        if n == 0: return 0
        dp_i_0, dp_i_1 = 0, float('-inf')
        for i in range(n):
            dp_i_0 = max(dp_i_0, dp_i_1 + prices[i] - fee)
            dp_i_1 = max(dp_i_1, dp_i_0 - prices[i])
        return dp_i_0
```

#### LC123.最多交易2次的股票交易（hard）
这里把k又返回了，这里要计算最多两次，则dp方程扩展为三维。  
这道题最重要的是初始值的设定，这里由于k=0和1，则对应在两次中都要设定相应的初始值。
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if len(prices) == 0: return 0
        n = len(prices)
        dp = [[[0 for _ in range(2)] for _ in range(3)] for _ in range(n+1)]
        dp[-1][0][0] = 0
        dp[-1][0][1] = float('-inf')
        dp[-1][1][0] = 0
        dp[-1][1][1] = float('-inf')
        for i in range(n):
            for k in range(2):
                dp[i][k][0] = max(dp[i - 1][k][0], dp[i - 1][k][1] + prices[i])
                dp[i][k][1] = max(dp[i - 1][k][1], dp[i - 1][k - 1][0] - prices[i])
        return dp[n - 1][1][0]
```

#### LC188.最多交易k次的股票交易（hard）
这里牵扯一个问题，就是k次必须要小于n/2，否则就跟无限次是一样的了。所以当k大于n/2时，调用122的解法，只有当k小于n/2时，采用三维数组来求。
```python
class Solution:
    def maxProfit_inf(self, prices: List[int]) -> int:
        if len(prices) == 0: return 0
        n = len(prices)
        dp = [[0 for _ in range(2)] for _ in range(n+1)]
        dp[-1][0] = 0
        dp[-1][1] = float('-inf')
        for i in range(n):
            dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] + prices[i])
            dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] - prices[i])
        return dp[n - 1][0]

    def maxProfit(self, k: int, prices: List[int]) -> int:
        if len(prices) == 0: return 0
        n = len(prices)
        if k > n/2: return self.maxProfit_inf(prices)

        dp = [[[0 for _ in range(2)] for _ in range(k+1)] for _ in range(n+1)]
        for j in range(k):
            dp[-1][j][0] = 0
            dp[-1][j][1] = float('-inf')

        for i in range(n):
            for j in range(k):
                dp[i][j][0] = max(dp[i - 1][j][0], dp[i - 1][j][1] + prices[i])
                dp[i][j][1] = max(dp[i - 1][j][1], dp[i - 1][j - 1][0] - prices[i])
        return dp[n - 1][k - 1][0]
```
