---
title: 时间复杂度与空间复杂度
date: 2017-9-20 12:11:51
tags:
categories: 算法
keywords:
description: 算法的时间复杂度是一个函数，它定量描述了该算法的运行时间。
---

算法的时间复杂度是一个函数，它定量描述了该算法的运行时间。用来评估算法运行效率的单位。
<!-- more -->

# 时间复杂度
>时间复杂度是用来估计算法运行时间的一个式子（单位） T(n) = O(n^2)

按数量级递增排列，常见的时间复杂度有：常数阶O(1),对数阶O(log2n),线性阶O(n), 线性对数阶O(nlog2n),平方阶O(n2)，立方阶O(n3),...， k次方阶O(nk),指数阶O(2n)。随着问题规模n的不断增大，上述时间复杂度不断增大，算法的执行效率越低。

- 一般来说,时间复杂度高的算法比复杂度底的算法慢.

```python
print('Hello World')    # O(1)

for i in range(n):    # O(n)
    print('Hello World')

for i in range(n):    # O(n²)
    for i in range(n):
        print('Hello World')

for i in range(n):    # O(n³)
    for j in rnage(n):
        for k in range(n):
            print('Hello World')
```


## 如何一眼判断时间复杂度
- 循环减半的过程 -->`O(logn)`
- 几次循环就是`n`的几次方的复杂度


## 时间复杂度排序

- 常见的时间复杂度

```
O(1)<O(logn)<O(n)<O(nlogn)<O(n²)<O(n²logn)<O(n³)
```

- 不常见的时间复杂度

```
O(n!) O(2n) O(nn) …
```


    几秒钟/O(1)
    几分钟/O(n)
    几小时/O(n²) // 2 平方
    高级： 函数 见进阶

## 判断时间复杂度

```python
# 1    O(1)  时间复杂度
print('Hello World')
print('Hello Python')
print('Hello Algorithm')

# 2    O(n²) 时间复杂度
for i in range(n):
    print('Hello World')
    for i in range(n):
        print('Hello World')

# 3    O(n²) 时间复杂度
for i in range(n):
    for j in range(i):
        print('Hello World')
```


```python
# 以2为底64的对数
n = 64
while n > 1:
    print(n)
    n = n // 2
# 时间复杂度 O(log2n) / O(logn)
# 每次少一半
```

![](https://i.imgur.com/A0jhF7s.jpg)



# 空间复杂度

>用来评估算法内存占用大小的式子    S(n) = O(n^2) 表示内存占用
时间复杂度比空间复杂度更重要

- 空间换时间

一个列表就是 O(n)
二维列表复杂度就是 O(n²)
...

