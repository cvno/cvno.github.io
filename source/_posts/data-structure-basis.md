---
title: 数据结构基础
date: 2017-12-26 20:58:31
tags: 
- 链表
- 栈
- 队列
- 哈希表
- 迷宫
categories: 数据结构
keywords:
description: 数据结构是设计数据以何种方式组织并存储在计算机中。
---
# 概念
数据结构是设计数据以何种方式组织并存储在计算机中。

比如：`列表`、`集合`与`字典`等都是一种数据结构。

详细的说：

- 物理层面：就是以什么样的物理存储方式
- 逻辑方式：列表，字典，集合，树

**线性数据结构，树形数据结构，图形数据结构，** 基本是线性数据结构

N.Wirth: “程序=数据结构+算法”

# 列表
列表：在其他编程语言中称为“数组”，是一种基本的数据结构类型。如果更学术一点的说法是“线性表”。

**数组与列表的不同之处：**
数组是定长的数组，如果开的内存空间长度为7，就不能再追加，只能存7个，并且这7个元素类型还必须是一样的

**数组：定长，元素类型统一。**

Python中的给一个列表，开一块连续内存空间，开的内存空间的长度不会刚好是列表的长度，一定会多，而内存中：变量指向的列表的元素存的是一个个内存地址（地址的格子才是真正存元素值的地方），每个元素占用的空间都是一样的，大部分编译型的语言直接存的值，而python存的不是值，而是值的内存地址。

Python中一直`append`值的原理：如果之前的空间不够，就再开时原来一倍的内存空间，然后把旧的删掉

>列表`li[2]`寻址的时候是`li+2*内存地址的字节`

32位机器一个地址4个子节
64位机器一个地址8个字节

**关于列表的问题：**

- 列表中元素使如何存储的？(上述)
- 列表提供了哪些基本的操作？(下标查找，插入，删除)
- 这些操作的时间复杂度是多少？
    - O(1)
    - O(n)(插入的时候，插入位置之后的值都需要往后挪)


# 链表
链表中每一个元素都是一个对象，每个对象称为一个节点，包含有数据域key和指向下一个节点的指针next。通过各个节点之间的相互连接，最终串联成一个链表。

为什么用链表？
链表的插入和删除特别快

**节点定义:**

```python
class Node(object):
    def __init__(self,item):
        self.item = item
        self.next = None

n1 = Node(1)
n2 = Node(2)
n3 = Node(3)

n1.next = n2
n2.next = n3

print(n1.next.item) # 2
print(n1.next.next.item) # 3
```

- 头节点

![单向链表](https://i.imgur.com/P1WJgQ8.jpg)

## 链表的遍历

![链表遍历](https://i.imgur.com/xvc5L8B.jpg)


```python
def traversal(head):
    curNode = head  # 临时用指针
    while curNode is not None:
        print(curNode.data)
        curNode = curNode.next 
```

## 链表节点的插入和删除
O1的时间复杂度

- 插入

```python
p.next = curNode.next
curNode.next = p
```
- 删除

```python
p = curNode.next
curNode.next = curNode.next.next
# 也可以 >> curNode.next = p.next
del p
```

## 建立链表

- 头插法

```python
def createLinkList(li):
    l = Node()
    for num in li:
        s = Node(Num)
        s.next = l.next
        l.next = s
    return l
```

- 尾插法

```python
def createLinkList(li):
    l = Node()
    r = l   # r 指向尾节点
    for num in li :
        s = Node(num)
        r.next = s
        r = s
```


## 双链表
双链表中每个节点有两个指针：一个指向后面节点、一个指向前面节点。

**节点定义：**

```python
class Node(object):
    def __init__(self,item = None):
        self.item = item
        self.next = None
        self.prior = None
```

### 双链表节点的插入和删除
- 插入

```python
p.next = curNode.next
curNode.next.prior = p
p.prior = curNode
curNode.next = p
```

- 删除

```python
p = curNode.next
curNode.next = p.next
p.next.prior = curNode
del p
```

## 链表分析

### 列表与链表
- 按元素值查找（链表二分是On的复杂度）
- 按下标查找（链表没有法用下表查找，李列表：O1，链表On）
- 在某元素后插入（列表：On，链表：O1）
- 删除某元素（列表：On，链表：O1）


树也是以链表的形式存

# 栈
栈(Stack)是一个数据集合，可以理解为只能在一端进行插入或删除操作的列表。

**特点：** 后进先出（last-in, first-out）LIFO

**概念：**

- 栈顶
- 栈底

**基本操作：**

- 进栈（压栈）：push
- 出栈：pop
- 取栈顶：gettop

**应用实例：**

- Word 的撤销操作（撤销的时候后边的操作）
- 重做

>两个栈,撤销栈出栈，重做栈压栈；重做操作是记录撤销操作的。

## 栈的简单实现(Python)
不需要自己定义，使用列表结构即可。

- 进栈函数：append
- 出栈函数：pop
- 查看栈顶函数：li[-1]

## 栈的应用 - 括号匹配问题
给一个字符串，其中包含小括号、中括号、大括号，求该字符串中的括号是否匹配。

- `()()[]{}`		匹配
- `([{()}])`		匹配
- `[](`		不匹配
- `[(])`		不匹配

代码补全，当栈是空的，栈才是合法的

- [代码实现括号匹配](https://github.com/cvno/Note/blob/master/1/code1.py)

# 队列
队列(Queue)是一个数据集合，仅允许在列表的一端进行插入，另一端进行删除。

- 进行插入的一端称为队尾(rear)，插入动作称为进队或入队
- 进行删除的一端称为队头(front)，删除动作称为出队
- 队列的性质：先进先出(First-in, First-out)
- 双向队列：队列的两端都允许进行进队和出队操作。

![队列](https://i.imgur.com/cct8beO.jpg)

![队列模型](https://i.imgur.com/ruUddhR.jpg)

## 队列的实现
**用Python的列表来实现出队复杂度太高**

```
使用方法：from collections import deque
- 创建队列：queue = deque(li)
- 进队：append
- 出队：popleft
- 双向队列队首进队：appendleft
- 双向队列队尾进队：pop
```

## 实现原理

- 初步设想：列表+两个下标指针
- 创建一个列表和两个变量，front变量指向队首，rear变量指向队尾。初始时，front和rear都为0。
- 进队操作：元素写到li[rear]的位置，rear自增1。
- 出队操作：返回li[front]的元素，front自减1。

![队列](https://i.imgur.com/VsKQCZb.jpg)

## 环形队列
环形队列：当队尾指针front == Maxsize + 1时，再前进一个位置就自动到0。
**实现方式：求余数运算**

- 队首指针前进1：front = (front + 1) % MaxSize
- 队尾指针前进1：rear = (rear + 1) % MaxSize
- 队空条件：rear == front
- 队满条件：(rear + 1) % MaxSize == front

### 代码实现

- 通过Python自带库
- [手写循环队列](https://gist.github.com/cvno/eeb1073a4fc23e42885a6112671f0545)

```python
import queue    # 线程同步
########  分割  ########
from collections import deque
queue = deque()
queue.append(1)
queue.append(2)
queue.append(3)
print( ())
print(queue.popleft())
########  分割  ########
queue.appendleft(2)
queue.pop()
```

# 哈希表
## Python中的集合与字典

哈希表查找
>哈希表（Hash Table，又称为散列表），是一种线性表的存储结构。通过把每个对象的关键字k作为自变量，通过一个哈希函数h(k)，将k映射到下标h(k)处，并将该对象存储在这个位置。

**例如：**数据集合{1,6,7,9}，假设存在哈希函数h(x)使得h(1) = 0, h(6) = 2, h(7) = 4, h(9) = 5，那么这个哈希表被存储为[1,None, 6, None, 7, 9]。

当我们查找元素6所在的位置时，通过哈希函数h(x)获得该元素所在的下标（h(6) = 2），因此在2位置即可找到该元素。

```python
li = [1,2,3,4]
s = {1,2,3,4} # 集合的效率更高
# 哈希函数设计的再好也避免不了碰撞
#
#
```
## 哈希冲突
哈希冲突：由于哈希表的下标范围是有限的，而元素关键字的值是接近无限的，因此可能会出现h(102) = 56， h(2003) = 56这种情况。此时，两个元素映射到同一个下标处，造成哈希冲突。

**解决哈希冲突**

- 拉链法（将所有冲突的元素用链表连接）
- 开放寻址法（通过哈希冲突函数得到新的地址）

![拉链法](http://onk83djzp.bkt.clouddn.com/2017-12-13-15131769752694.jpg)

## 字典
**在Python中的字典：**

```
a = {'name': 'Alex', 'age': 18, 'gender': 'Man'}
```
使用哈希表存储字典，通过哈希函数将字典的键映射为下标。假设h(‘name’) = 3, h(‘age’) = 1, h(‘gender’) = 4，则哈希表存储为[None, 18, None, ’Alex’, ‘Man’]

在字典键值对数量不多的情况下，几乎不会发生哈希冲突，此时查找一个元素的时间复杂度为O(1)。

# 迷宫问题

给一个二维列表，表示迷宫（0表示通道，1表示围墙）。给出算法，求一条走出迷宫的路径。

## 栈

- [栈-方案](https://github.com/cvno/Note/blob/master/1/maze.py)

深度优先 **DFS（Depth[栈]-First-Search）**

## 队列

- [队列-方案](https://github.com/cvno/Note/blob/master/1/code2.py)

使用二维列表存储多条路径
**如何是打印出路径是难点**

![迷宫](http://onk83djzp.bkt.clouddn.com/15114913815577.jpg)

```
maze = [
    [1,1,1,1,1,1,1,1,1,1],
    [1,0,0,1,0,0,0,1,0,1],
    [1,0,0,1,0,0,0,1,0,1],
    [1,0,0,0,0,1,1,0,0,1],
    [1,0,1,1,1,0,0,0,0,1],
    [1,0,0,0,1,0,0,0,0,1],
    [1,0,1,0,0,0,1,0,0,1],
    [1,0,1,1,1,0,1,1,0,1],
    [1,1,0,0,0,0,0,0,0,1],
    [1,1,1,1,1,1,1,1,1,1]
]
```

本文代码及 md 文件 [Github](https://github.com/cvno/Note/tree/master/1)

