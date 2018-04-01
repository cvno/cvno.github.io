---
title: 数据结构：树
date: 2017-12-25 15:04:18
tags: 
categories: 数据结构
keywords: 树，二叉树
description: 认识树与二叉树
---

# 树

![树（数据结构）](https://i.imgur.com/JH2f8TO.jpg)

- 是一种数据结构（如：目录结构） 
- 是一种可以递归定义的数据结构
- 是由 n 个节点组成的集合
    - 如果 n=0 ，那么是一颗空树
    - 如果 n>0 ，那么存在 1 个节点作为树的根节点，其他节点可以分为 m 个集合，每个集合本身又是一棵树。

**概念：**

- 根节点（最顶端的节点）、叶子节点（没有孩子的节点，结构的最末端）
- 树的深度／高度（也就是树的层数）
- 节点度（也就是这个节点分了多少叉）
- 树的度（所有节点度的最大值）
- 孩子节点/父节点（看字面理解）
- 子树（根节点的字节点都是独立的树）

# 二叉树
度不超过 2 的树（节点最多有两个叉），它的孩子是有顺序的：左孩子，右孩子。
![二叉树的多种形态](https://i.imgur.com/Fq7IcbA.jpg)

重点：满二叉树，完全二叉树


## 二叉树的存储方式
1. 链式存储方式
2. 顺序存储方式（列表）
3. 面向对象的存储方式

![](https://i.imgur.com/X5OMYC1.jpg)

父节点和左孩子节点的编号下标有什么关系？
>0-1 1-3 2-5 3-7 4-9
规律：i = 2i+1

父节点和右孩子节点的编号下标有什么关系？
>0-2 1-4 2-6 3-8 4-10
规律：i = 2i+2

比如，我们要找根节点左孩子的左孩子：（0\*2+1）\*2+1 = 3 （下标） 所以是`6`

## 面向对象的存储方式

```python
class BinTreeNode:
    def __init__(self, data):
        self.data = data
        self.lchild = None
        self.rchild = None

k = BinTreeNode('K')
g = BinTreeNode('G')
c = BinTreeNode('C')
a = BinTreeNode('A')
b = BinTreeNode('B')
d = BinTreeNode('D')
e = BinTreeNode('E')
f = BinTreeNode('F')
h = BinTreeNode('H')


root = a
a.lchild = b
a.rchild = e
b.lchild = h
b.rchild = f
f.lchild = d
e.rchild = c
c.lchild = k
c.rchild = g
```
![二叉树](https://i.imgur.com/umYC031.jpg)

- 前序遍历 

```python


def PreBianli(root):
    p = root
    if p:
        print(p.data, end=' ')
        PreBianli(p.lchild)
        PreBianli(p.rchild)

# PreBianli(root)   # A B H F D E C K G    前序遍历
```

- 中序遍历

```python
def MidBianli(root):
    p = root
    if p:
        MidBianli(p.lchild)
        print(p.data, end=' ')
        MidBianli(p.rchild)
# MidBianli(root)   # H B D F A E K C G    中序遍历
```

- 后序遍历

```python
def PostBianli(root):
    p = root
    if p:
        PostBianli(p.lchild)
        PostBianli(p.rchild)
        print(p.data, end=' ')
# PostBianli(root)  # H D F B K G C E A    后序遍历
```
*在根据任意两个序列来推测第三个序列的时候，有中序比较好推测，因为能一眼看出二叉树的根*

![前序遍历](https://i.imgur.com/4pl3DJs.jpg)
![中序遍历](https://i.imgur.com/jJGlzo8.jpg)
![后续遍历](https://i.imgur.com/GyEjLF3.jpg)

#### 层级遍历

```python
def LevelBianli(root):
    curLevel = [root]
    nextLevel = []
    while len(curLevel)>0:
        for node in  curLevel:
            print(node.data, end=' ')
            if node.lchild:
                nextLevel.append(node.lchild)
            if node.rchild:
                nextLevel.append(node.rchild)
        curLevel = nextLevel
        nextLevel = []

LevelBianli(root)   # A B E H F C D K G
```

## 二叉树小结
- 二叉树是度不超过 2 的树
- 满二叉树与完全二叉树
- （完全）二叉树可以用列表来存储，通过规律可以从父亲找到孩子或者孩子找到父亲
- 二叉树遍历方式 : `前序遍历` `中序遍历` `后序遍历`


