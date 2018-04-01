---
title: 列表查找
date: 2017-9-21 12:09:36
tags:
categories: 算法
keywords:
description: 顺序查找与二分查找
---

<!-- more -->

# 顺序查找


- 从列表第一个元素开始，顺序进行搜索，直到找到为止。

```python
li = [1,2,3]
index(1)    # 顺序查找

# 顺序查找 ipython  O(n) 复杂度
import random
n = 10000
li = list(range(n))
random.shuffle(li)
%timeit li.index(3200)
#221 µs ± 11.1 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)
```

# 二分查找
>只能用于有序列表

从有序列表的候选区`data[0:n]`开始，通过对待查找的值与候选区中间值的比较，可以使候选区减少一半。`O(logn)` 复杂度

```python
# 二分查找
# 循环版本
# def bin_search(li,low,high):
def bin_search(li, val):
    '''循环二分 时间复杂度 O(logn)'''
    low = 0
    high = len(li) - 1
    while low <= high:
        mid = (low + high) // 2
        if li[mid] == val:
            return mid
        elif li[mid] < val:
            low = mid + 1
        else:  # >
            high = mid - 1
    return None
# 5.6 µs ± 441 ns per loop (mean ± std. dev. of 7 runs, 100000 loops each)

# 递归版本  递归需要切换进出栈
def bin_search_rec(data_set,value,low,high):
    ''' 尾递归 和 非递归的效率基本一样 '''
    if low <= high:
        mid = (low+high) // 2
        if data_set[mid] == value:
            return mid
        elif data_set[mid] > value:
            # 尾递归不用切换出栈
            return bin_search_rec(data_set,value,low,mid-1)
        else:
            return bin_search_rec(data_set,value,mid+1,high)
    else:
        return

# %timeit l1.index(3200)
# l1.sort()   # 排序
# %timeit bin_search(l1,3200)
print(bin_search(l1,4000))
```


刷题：Letcode
[34. Search for a Range (二分查找升级版)](https://leetcode.com/problems/search-for-a-range/?tab=Description)
[1. Two Sum](https://leetcode.com/problems/two-sum/?tab=Description)

# 习题
1  [34. Search for a Range (二分查找升级版)](https://leetcode.com/problems/search-for-a-range/?tab=Description)

```python
def bin_search(li, val):
    '''循环二分 时间复杂度 O(logn)'''
    low = 0
    high = len(li) - 1
    while low <= high:
        mid = (low + high) // 2
        if li[mid] == val:
            a = mid
            b = mid
            while li[a] = value and a >= 1:
                a -= 1
            while li[b] = value and b < len(li):    # b <= len(li) - 1
                b += 1
            return (a+1,b-1)
        elif li[mid] < val:
            low = mid + 1
        else:  # >
            high = mid - 1
    return None
```

2 . [1. Two Sum](https://leetcode.com/problems/two-sum/?tab=Description)

```python
def two_sum(nums, target):
    l = len(nums)
    for i in range(l):
        for j in range(i+1,l):
            print(nums[i],nums[j])
            if nums[i] + nums[j] == target:
                return (i,j)
    return None
            
print(two_sum([2, 7, 11, 15],9))
```
或者

```python
def bin_search(data_set, value):
    low = 0
    high = len(data_set) - 1
    while low <= high:
        mid = (low + high) // 2
        if data_set[mid] == value:
            return mid
        elif data_set[mid] > value:
            high = mid - 1
        else:
            low = mid + 1

def two_sum_2(li, target):
    li.sort()
    for i in range(len(li)):
        b = target - li[i]
        j = bin_search(li, b)
        if j != None and i != j:
            return i, j

print(two_sum_2([2, 7, 11], 14))

def two_sum_3(li, target):
    li.sort()   # nlogn
    i = 0
    j = len(li) - 1
    while i<j:
        sum = li[i]+li[j]
        if sum > target:
            j-=1
        elif sum < target:
            i+=1
        else: #sum==target
            return (i,j)
    return None
```

# 扩展
[1. Two Sum](https://leetcode.com/problems/two-sum/?tab=Description) 如果是 3 个数 就把第一个数固定, 后面的列表用 two_sum_3 来计算
> 如果这样时间复杂度
nlogn + n²
最终的时间复杂度是 n²


如果用二分查找, 就需要先排序, 定住两个数, 排序(nlogn) + `定住两个数(n²)` `二分(n²logn)` 最终的复杂度是 `n²logn`

