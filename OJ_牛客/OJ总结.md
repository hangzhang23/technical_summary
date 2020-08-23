## 总结性归纳下牛客和塞码网出现的OJ输入和输出情况。
### 输入为int
1. ```A+B```输入包括两个正整数a和b，输入数据包含多组。
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
3. 输入包括两个正整数a，b，输入数据有多组，如果输入为0，则结束输入。
```python
while True:
    a,b = map(int,input().split())
    if a== 0 and b == 0:
        break
    else:
        print(a+b)

```
4.（计算一系列数的和）输入包括多组数据。每组数据一行，每行的第一个整数为整数的个数n，n为0得时候结束输入。接下来的n个正整数，即需要求和的每个正整数。
```python
while True:
    a = [int(each) for each in input().split()]
    if a[0]==0:
        break
    print(sum(a[1:]))
```
5.（计算一系列数的和）输入的第一行包括一个正整数t，表示数据组数。接下来的t行，每行一组数据。每行的第一个整数为整数的个数n，接下来的n个正整数，即需要求和的每个正整数。
```python
n = int(input())
for _ in range(n):
    numList = list(map(int, input().split()))
    print(sum(numList[1:]))
```
6.（计算一系列数的和）输入数据有多组，每行表示一组输入数据。每行的第一个整数为整数的个数n。接下来的n个正整数，即需要求和的每个正整数。
```python
import sys
lines = sys.stdin.readlines()
for line in lines:
    numList = list(map(int, line.split()))
    print(sum(numList[1:]))
```
7.（计算一系列数的和）输入数据有多组，每行表示一组输入数据。每行不定有n个整数，空格隔开。
```python
import sys
for line in sys.stdin:
    a  = line.split()
    ret = 0
    for i in range(len(a)):
        ret += int(a[i])
    print(ret)
```

### 输入为字符串
1. 对输入的字符串进行排序后输出。
```python
while True:
    try:
        n = input()
        strList = list(map(str, input().split()))
        strList.sort()
        print(' '.joint(strList))
    except:
        break
```
2. 对输入的字符串进行排序后输出。多个测试用例，每个测试用例一行。每行用空格隔开，有n个字符。
```python
while True:
    try:
        arr = list(map(str, input().split()))
        arr.sort()
        print(' '.join(arr))
    except:
        break
```
3. 对输入的字符串进行排序后输出。多个测试用例，每个测试用例一行。每行通过，隔开，有n个字符。
```python
while True:
    try:
        a = input().split(",")
        a.sort()
        print(",".join(a))
    except:
        break
```
