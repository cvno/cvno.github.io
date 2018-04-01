---
title: TOP 榜单算法（nlargest）
date: 2017-12-26 13:52:13
tags: 
- heapq
- nlargest
categories:
keywords:
description: 
---
# 问题

现在有n个数（n>10000），设计算法，按大小顺序得到前~~10~~m大的数。

<!-- more -->
- 应用场景：榜单TOP 10

# 解决方法

1. 先排序，取前 10 个数 O(nlogn)
2. 只留前 10 个数，开一个长度为 10 的列表，用插入排序取出 10 个数，来一个数和列表最后一个数比较，如果比它更小就扔掉 O(nm)不适用与 m 特别大的时候
3. 堆 O(nlogm)

用堆解决思路：

1. 取列表前m个元素建立一个小根堆。堆顶就是目前第m大的数。
2. 依次向后遍历原列表，对于列表中的元素，如果小于堆顶，则忽略该元素；如果大于堆顶，则将堆顶更换为该元素，并且对堆进行一次调整；
3. 遍历列表所有元素后，倒序弹出堆顶。


```python
def sift(data, low, high):
    """
    调整函数
    data: 列表
    low：待调整的子树的根位置
    high：待调整的子树的最后一个节点的位置
    """
    i = low
    j = 2 * i + 1
    tmp = data[i]
    # i指向空位置
    while j<=high:      #领导已经撸到底了
        if j != high and data[j] < data[j+1]:
            j += 1
        #j指向数值大的孩子
        if tmp < data[j]:   #如果小领导比撸下来的大领导能力值大
            data[i] = data[j]
            i = j
            j = 2*i+1
        else:
            break       #撸下来的领导比候选的领导能力值大
    data[i] = tmp

def topn(li, n):
    heap = li[0:n]
    # 建堆
    for i in range(n // 2 - 1, -1, -1):
        sift(heap, i, n - 1)
        # 遍历
    for i in range(n, len(li)):
        if li[i] > heap[0]:
            heap[0] = li[i]
            sift(heap, 0, n - 1)
    # 出数
    for i in range(n - 1, -1, -1):
        heap[0], heap[i] = heap[i], heap[0]
        sift(heap, 0, i - 1)
```

- Python内置模块——heapq

```python
import heapq
# 利用heapq模块实现堆排序
def heapsort(li):
    h = []
    for value in li:
        heapq.heappush(h, value)# 建堆, 并自动排序
    return [heappop(h) for i in range(len(h))]

heapsort([6,8,1,9,3,0,7,2,4,5]) # [0,2,1,3,5,6,7,9,4,8]

# ============== 分割线 ==============
# 利用heapq模块实现取top-k
heapq.nlargest(100, li)
```

优先队列：一些元素的集合，POP操作每次执行都会从优先队列中弹出最大（或最小）的元素。
>堆——优先队列

# 参考
- http://python.usyiyi.cn/translate/python_352/library/heapq.html

```python
# 位运算
# >> 除以2
2 >> 1 # 1 
4 >> 1 # 2
8 >> 1 # 4
# << 乘以 2
2 << 1  # 4
```

