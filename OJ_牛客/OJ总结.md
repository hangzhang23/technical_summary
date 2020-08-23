### 总结性归纳下牛客和塞码网出现的OJ输入和输出情况。
1. ```A+B```
```python
import sys
line = sys.stdin.readlines()

for line in lines:
    numlist = list(map(int, line.split()))
    print(sum(numlist))
```  

2. 输入第一行包括一个数据组数t，接下来每行包括两个正整数a，b，求a+b的结果。
```python
import sys
lines = sys.stdin.readlines()
n = int(lines[0])
for i in range(n):
    numList = list(map(int, lines[i+1].split()))
    print(sum(numList))
```
