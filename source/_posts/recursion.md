---
title: 递归
date: 2017-12-22 20:48:26
tags:
categories: 算法
keywords: 递归
description: 在函数内部，可以调用其他函数。如果一个函数在内部调用自身本身，这个函数就是递归函数。
---

在函数内部，可以调用其他函数。如果一个函数在内部调用自身本身，这个函数就是递归函数。

**特点：**

- 调用自身
- 结束条件

```python
def func1(x):
    print(x)
    func1(x-1)

# func1(3)  # 死递归 没有结束条件

def func2(x):
    if x > 0:
        print(x)
        func2(x+1)

# func2(3)    # 3,4,5,6...  有结束条件，如果是正数还是会陷入死递归

def func3(x):
    if x > 0:
        print(x)
        func3(x-1)

# func3(3)  # 3,2,1   有结束条件

def func4(x):
    if x > 0:
        func4(x-1)
        print(x)

# func4(3)    # 1,2,3 有结束条件
```

递归效率不高，递归层次过多会导致栈溢出（在计算机中，函数调用是通过栈（stack）这种数据结构实现的，每当进入一个函数调用，栈就会加一层栈帧，每当函数返回，栈就会减一层栈帧。由于栈的大小不是无限的，所以，递归调用的次数过多，会导致栈溢出）

# 练习

![dog递归](https://i.imgur.com/Z1qnoKw.jpg)

```python
def func(depth):
    if depth == 0:
        print('我的小鲤鱼',end='')   # 取消换行
    else:
        print('抱着',end='')
        func(depth-1)
        print('的我',end='')

func(3) # 抱着抱着抱着我的小鲤鱼的我的我的我
```

```python
# 利用递归函数计算阶乘
# N! = 1 * 2 * 3 * ... * N
def fact(n):
    if n == 1:
        return 1
    return n * fact(n-1)

print('fact(1) =', fact(1))
print('fact(5) =', fact(5))
print('fact(10) =', fact(10))

# 利用递归函数移动汉诺塔:
def move(n, a, b, c):
    if n == 1:
        print('move', a, '-->', c)
    else:
        move(n-1, a, c, b)
        move(1, a, b, c)
        move(n-1, b, a, c)

move(4, 'A', 'B', 'C')
```


