---
title: 常用排序算法
date: 2017-12-22 15:22:38
tags: Python
categories: 算法
keywords: 
description: 冒泡排序、选择排序、插入排序、快速排序、堆排序、归并排序、计数排序、希尔排序
---

**重点:**

- 有序区
- 无序区

# 冒泡排序（BUB）
>列表每两个相邻的数, 如果前边的比后边的大, 那么交换这两个数

**冒泡排序算法的流程如下：**

1. 比较相邻的元素。如果第一个比第二个大，就交换他们两个。
2. 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。在这一点，最后的元素应该会是最大的数。
3. 针对所有的元素重复以上的步骤，除了最后一个。
4. 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。

![冒泡排序](https://i.imgur.com/T0nk855.gif)

**关键点:**  趟, 无序区

## code

```python
# O(n²) 时间复杂度
def bubble_sort(li):
    if len(li) <= 1:
        return li
    for i in range(len(li)-1): # i 是趟
        for j in range(len(li)-i - 1): # j 是指针
            if li[j] > li[j+1]:
                li[j], li[j+1] = li[j+1] , li[j]
    return li
li = list(range(10000))
import random as rd
rd.shuffle(li) # 打乱顺序
print(li)
print(bubble_sort(li))
```
## 优化版

```python
def bubble_sort(li):
    if len(li) <= 1:
        return li
    for i in range(len(li) - 1):  # i 是趟
        exchange = Flase
        for j in range(len(li) - i - 1):  # j 是指针
            if li[j] > li[j + 1]:
                li[j], li[j + 1] = li[j + 1], li[j]
                exchange = True
        if not exchange:
            break
    return li
```

| 空间时间复杂度 | O(1) |
| --- | --- |
| 最坏时间复杂度 | O(n²) |
| 最优时间复杂度 | O(n) |
| 平均时间复杂度 | O(n²) |


# 选择排序（SEL）
每一次从待排序的数据元素中选出最小（或最大）的一个元素，存放在序列的起始位置，直到全部待排序的数据元素排完。
![选择排序](https://i.imgur.com/Y7JK85g.gif)

```python
def select_sort(li):
    for i in range(len(li) - 1):  # i 是趟
        min_doc = i
        # 找i位置到最后位置范围内最小的数
        for j in range(i, len(li)):  # i可以换成i+1,省去和自己比
            if li[j] < li[min_doc]:
                min_doc = j
        # 和无序区第一个数作交换
        # 可以加上 i==min_loc 的判断,省去和自己换
        if min_doc != i:
            li[min_doc], li[i] = li[i], li[min_doc]
        '''
        i, j, min_doc 都是下标
        '''
    return li
```
| 空间时间复杂度 | O(1) |
| --- | --- |
| 最坏时间复杂度 | O(n²) |
| 最优时间复杂度 | O(n²) |
| 平均时间复杂度 | O(n²) |


# 插入排序（INS）
插入排序每次取出数组后半部分的第一个元素，在排好序的前半部分中，为其找到最合适的位置并进行插入(扑克牌)
![插入排序](https://i.imgur.com/UzdoF4Z.gif)

- 列表被分为有序区和无序区两个部分。最初有序区只有一个元素。
- 每次从无序区选择一个元素，插入到有序区的位置，直到无序区变空。

**插入排序算法的流程如下：**

1. 从第一个元素开始，该元素可以认为已经被排序
2. 取出下一个元素，在已经排序的元素序列中从后向前扫描
3. 如果该元素（已排序）大于新元素，将该元素移到下一位置
4. 重复步骤 3，直到找到已排序的元素小于或者等于新元素的位置
5. 将新元素插入到该位置后
6. 重复步骤 2~5

**关键点：**

- 摸到的牌
- 手里的牌 (有序)

## code

```python
def insert_sort(li):
    if len(li) == 1:
        return li
    for i in range(1, len(li)):  # i代表每次摸到的牌的下标
        tmp = li[i]
        j = i - 1  # j代表手里最后一张牌的下标
        while j >= 0 and tmp < li[j]: # 摸到的牌比手牌最后的牌小
            li[j + 1] = li[j] # 把最大的手牌往后挪动
            j -= 1
        li[j + 1] = tmp # 摸到的牌比手牌最后的牌大
    return li
```

| 空间时间复杂度 | O(1) |
| --- | --- |
| 最坏时间复杂度 | O(n²) |
| 最优时间复杂度 | O(n²) |
| 平均时间复杂度 | O(n²) |

# 快速排序（QUI）
博主看动图不是很理解, 建议看 [这里](http://bbs.ahalei.com/thread-4419-1-1.html)
![快速排序](https://upload.wikimedia.org/wikipedia/commons/6/6a/Sorting_quicksort_anim.gif)
![快速排序](https://i.imgur.com/mEG5lt2.gif)

**快速排序算法的流程如下：**

- 取一个元素p（第一个元素），使元素p归位；
- 列表被p分成两部分，左边都比p小，右边都比p大；
- 递归完成排序。

**关键点：**

- 整理(让元素归位)
- 递归

```python
def partition(data, left, right):
    '''
    partition:归位函数
    右手左手一个慢动作
    右手左手慢动作重播
    '''
    tmp = data[left] # 取基准数
    while left < right:
        # 如果需要降序排序的话, 就把 data[right] >= tmp 中的小于等于改为大于等于
        while left < right and data[right] >= tmp:
            right -= 1 # 左移
        if left < right: # 如果上面的循环是因为找到了 right 小于 tmp 的数而跳出循环
            data[left] = data[right] # 把小于 tmp 的这个元素放到 tmp 的位置上
        # 如果需要降序排序的话, 就把 data[right] >= tmp 中的大于等于改为小于等于
        while left < right and data[left] <= tmp:
            left += 1 # 右移
        data[right] = data[left] # 把大于 tmp 的这个元素放到 tmp 的位置上
    data[left] = tmp # 那个 mid 回来
    return left
    
def _quick_sort(data, left, right):
    if left < right:
        mid = partition(data, left, right)
        _quick_sort(data, left, mid - 1)
        _quick_sort(data, mid + 1, right)

@cal_time
def quick_sort(data):
    return _quick_sort(data,0,len(data)-1)
```
## 优化版

```python
# 来自知乎 @风满楼
def quick_sort(lists, left, right):
    if left > right:
        return lists
    low, high = left, right
    key = lists[left]  # key即是基准数
    while left < right:
        while left < right and lists[right] >= key:
            right -= 1 # 左移
        lists[left] = lists[right] 
        while left < right and lists[left] <= key:
            left += 1 # 右移
        lists[right] = lists[left]
    lists[right] = key
    quick_sort(lists, low, left - 1)
    quick_sort(lists, right + 1, high)
    return lists

quick(data,0,len(data)-1)
```

```python
# 快排精简版
def quick(data):
    if data == []:
        return []
    else:
        pivot = data[0]
        lesser = [x for x in data[1:] if x <= pivot]
        greater = [x for x in data[1:] if x > pivot]
    return quick(lesser) + [pivot] + quick(greater)
```

## 问题
某些极端的情况下复杂度非常高, 如：

```
9 8 7 6 5 4 3 2 1
```
出现的概率不多, 属于极端情况, 解决方法: 选基准的时候随机选一个数与第一个数交换。

| 空间时间复杂度 | 根据实现的方式不同而不同 |
| --- | --- |
| 最坏时间复杂度 | O(n²) |
| 最优时间复杂度 | O(nlogn) |
| 平均时间复杂度 | O(nlogn) |

PS: 看到一个最狠的快排

```python
# https://github.com/qiwsir/algorithm/blob/master/quick_sort.md
qs = lambda xs : ( (len(xs) <= 1 and [xs]) or [ qs( [x for x in xs[1:] if x < xs[0]] ) + [xs[0]] + qs( [x for x in xs[1:] if x >= xs[0]] ) ] )[0]
```

## 参考资料

- [Ele - A面](http://ictar.github.io/2015/12/08/%E4%B9%9D%E5%A4%A7%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95%E5%8F%8A%E5%85%B6Python%E5%AE%9E%E7%8E%B0%E4%B9%8B%E5%BF%AB%E9%80%9F%E6%8E%92%E5%BA%8F/)
- <http://bbs.ahalei.com/thread-4419-1-1.html>
- http://blog.csdn.net/v_july_v/article/details/6116297
- https://www.zhihu.com/question/26786398
- https://hellolynn.hpd.io/2017/08/03/%E5%BF%AB%E9%80%9F%E6%8E%92%E5%BA%8F-quick-sort/
- https://github.com/qiwsir/algorithm/blob/master/quick_sort.md

# 堆排序（HEAP）
堆排序用的是[树](/2017/12/data-structure-tree.html)的结构
![树（数据结构）](https://i.imgur.com/JH2f8TO.jpg)

## 堆
- 大根堆：一棵完全二叉树，满足任一节点都比其孩子节点大
- 小根堆：一棵完全二叉树，满足任一节点都比其孩子节点小

![大小根堆](https://i.imgur.com/ShbrOiR.jpg)


**假设：节点的左右子树都是堆，但自身不是堆**
>当根节点的左右子树都是堆时，可以通过一次向下的调整来将其变换成一个堆。

![调整](https://i.imgur.com/Vyt45u6.gif)

**堆排序过程:**

1. 建立堆
2. 得到堆顶元素，为最大元素
3. 去掉堆顶，将堆最后一个元素放到堆顶，此时可通过一次调整重新使堆有序。
4. 堆顶元素为第二大元素。
5. 重复步骤3，直到堆变空。

- 构建堆

先从最小的子树开始看, 最后一步看整个的堆; 从最后一个非叶子节点为根的子树开始做调整
![构建堆](https://i.imgur.com/VF7ia3X.gif)

- 挨个出数

![挨个出数](https://i.imgur.com/4fYb7L4.gif)


## code

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

def heap_sort(data):
    n = len(data)
    # 建堆 从最后一个非叶子节点所以是 -1(2) 列表倒序
    # n//2-1 找最后一个非叶子节点
    # -1(1) 顾前不顾后
    for i in range(n//2-1, -1, -1):
        sift(data, i,  n - 1) # 这里的 n-1 是把所有子树的 high 都设置成整个堆的 high
    # 挨个出数
    for high in range(n - 1, -1, -1):
        data[0], data[high] = data[high], data[0]
        sift(data, 0, high - 1)
```

| 空间时间复杂度 | 	O(n)，O(1) |
| --- | --- |
| 最坏时间复杂度 | O(nlogn) |
| 最优时间复杂度 | O(nlogn) |
| 平均时间复杂度 | O(nlogn) |

## 引用
- [数据结构：树](/数据结构：树/)
- https://www.cnblogs.com/chengxiao/p/6129630.html
- http://bubkoo.com/2014/01/14/sort-algorithm/heap-sort/
- http://wuchong.me/blog/2014/02/09/algorithm-sort-summary/

# 归并排序（MER）
![归并排序](https://i.imgur.com/7sbKXQt.gif)
![归并排序](https://i.imgur.com/FN392sX.gif)
**归并排序思路:**

1. 分解：将列表越分越小，直至分成一个元素。
2. 一个元素是有序的。
3. 合并：将两个有序列表归并，列表越来越大。

![归并排序流程](https://i.imgur.com/dXgFcwQ.png)

1. 递归地将数组划分为两部分
2. 直到两个子数组元素都为1时，返回并将两个数组进行排序融合
3. 逐步返回，并递归融合，最终使得数组有序

## code

```python
def merge(data, low, mid, high):
    '''一次归并'''
    i = low
    j = mid + 1
    ltmp = []  # 临时列表
    while i <= mid and j <= high:
        if data[i] <= data[j]:
            ltmp.append((data[i]))
            i += 1
        else:  # data[i] > data[j]
            ltmp.append(data[j])
            j += 1
    while i <= mid:
        ltmp.append(data[i])
        i += 1
    while j <= high:
        ltmp.append(data[j])
        j += 1
    data[low:high + 1] = ltmp


def mergesort(data, low, high):
    '''归并排序'''
    if low < high:
        mid = (low + high) // 2 # 获取中间位置
        mergesort(data, low, mid) # 分解左半部分
        mergesort(data, mid + 1, high) # 分解右半部分
        merge(data, low, mid, high) # 归并
    return data
```
## 加深理解

```python
def func(x):
    if x > 1:
        y = x // 2
        func(y)
        func(y)
        print(y)
func(20)
# 看最后的输出 画图
```

或者结合[递归](/递归/)

![递归输出](https://i.imgur.com/XBeGCIP.png)

| 空间时间复杂度 | 	O(n)|
| --- | --- |
| 最坏时间复杂度 | O(nlogn) |
| 最优时间复杂度 | O(n) |
| 平均时间复杂度 | O(nlogn) |

- 快速排序、堆排序、归并排序 - 小结

三种排序算法的时间复杂度都是O(nlogn)

- 运行时间:

`快速排序` < `归并排序` < `堆排序`

三种排序算法的缺点：

|快速排序|极端情况下排序效率低|
| --- | --- |
|归并排序|需要额外的内存开销|
|堆排序|在快的排序算法中相对较慢|

# 计数排序（COU）

![计数排序](https://i.imgur.com/piK8FYS.gif)

>题: 现在有一个列表，列表中的数范围都在 0 到 100 之间，列表长度大约为 100 万。设计算法在 O(n) 时间复杂度内将列表进行排序。


```python
def count_sort(data, maxnum = 100):
    '''计数排序  O(n)'''
    count = [0 for i in range(maxnum+1)]
    result = []
    for i in data:
        count[i] += 1
    for num,count in enumerate(count):
        for i in range(count):
            result.append(num)
# 或
def count_sort(data, max_num):
    count = [0 for i in range(max_num + 1)]
    for num in data:
        count[num] += 1
    i = 0
    for num, m in enumerate(count):
        for j in range(m):
            data[i] = num
            i += 1
```

因为要开额外的内存空间，所以使用并不多。计数排序限定元素不会太大的时候，如：年龄可以使用计数排序

# 希尔排序（SHE）

希尔排序是一种分组插入排序算法。O(1.3n)

![希尔排序](https://i.imgur.com/s1gQYYA.gif)

1. 以数组元素长度的一半做为初始步长gap，将数组划分为gap个子数组
2. 循环切换遍历子数组，在子数组内分别进行插入排序
3. 将gap更新为gap/2，重复上述步骤1，2，直到gap为1

![步长为4的shell sort示例](https://i.imgur.com/HXBOgwr.jpg)


**希尔排序思路：**

1. 先取一个正整数 d<sub>1</sub>(d<sub>1</sub> < n)，把全部记录分成 d<sub>1</sub> 个组，所有距离为 d<sub>1</sub> 的倍数的记录看成一组，然后在各组内进行插入排序
2. 然后取 d<sub>2</sub>(d<sub>2</sub> < d<sub>1</sub>)
3. 重复上述分组和排序操作；直到取 d<sub>i</sub> = 1(i >= 1) 位置，即所有记录成为一个组，最后对这个组进行插入排序。一般选 d1 约为 n/2，d<sub>2</sub> 为 d<sub>1</sub> /2， d<sub>3</sub> 为 d<sub>2</sub>/2 ，…， d<sub>i</sub> = 1。


希尔排序每趟并不使某些元素有序，而是使整体数据越来越接近有序；最后一趟排序使得所有数据有序。


```python
# 修改插入排序
def insert_sort_gap(data, gap):
    for i in range(gap, len(data)):
        tmp = data[i]
        j = i - gap
        while j >= 0 and tmp < data[j]:
            data[j + gap] = data[j]
            j = j - gap
        data[j + gap] = tmp

def shell_sort(data):
    '''希尔排序'''
    d = len(data) // 2
    while d > 0:
        insert_sort_gap(data,d)
        d = d // 2
    return data
```
## 优化版

```python
def shell_sort(data):
    n = len(data)
    gap = len(data) // 2
    while gap > 0:
        for i in range(gap, n):
            tmp = data[i]
            j = i - gap
            while j >= 0 and tmp < data[j]:
                data[j + gap] = data[j]
                j -= gap
            data[j + gap] = tmp
        gap = gap // 2
    return data
```

# 后记
## 排序算法指标

![排序算法指标](https://i.imgur.com/kkVSVGH.jpg)
## 排序的稳定性
排序关键字相同的情况下，对象的相对位置不变
## 计时装饰器

```python
def cal_time(func):
    def wrapper(*args, **kwargs):
        t1 = time.time()
        x = func(*args, **kwargs)
        t2 = time.time()
        print("%s running time %s secs." % (func.__name__, t2 - t1))
        return x
    return wrapper
```
## 参考资料
- 博客部分图片截取自 <https://visualgo.net/zh/sorting>
- <http://bubkoo.com>
- <http://chenyvehtung.github.io/2017/02/26/sort-algorithms.html>
- [维基百科-排序算法](https://zh.wikipedia.org/wiki/Category:%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95)

