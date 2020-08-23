### 总结性归纳下牛客和塞码网出现的OJ输入和输出情况。
1. ```A+B```
```python
import sys
line = sys.stdin.readlines()

for line in lines:
    numlist = list(map(int, line.split()))
    print(sum(numlist))
```  
