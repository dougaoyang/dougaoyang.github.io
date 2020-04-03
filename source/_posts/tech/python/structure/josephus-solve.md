---
layout: post
title: 约瑟夫（Josephos）问题以及解法
date: 2020-04-03
categories:
- 技术
tags:
- python
- 数据结构
description: 假设n个人围坐一圈，现在要求从第k个人开始报数，报到第m个人退出，然后从下一个人开始继续报数并按照同样规则退出，直至所有人退出。要求按照顺序输出退出人的编号。
mathjax: true
---

## Josephos问题
假设n个人围坐一圈，现在要求从第k个人开始报数，报到第m个人退出，然后从下一个人开始继续报数并按照同样规则退出，直至所有人退出。要求按照顺序输出退出人的编号。


## 基于顺序表的解法

当确定退出的人后，就将其编号的元素从列表中删除，这样列表就会越来越短，直至0结束。

```python
def josephos_l(n, k, m):
    people = list(range(1, n+1))
    i = k-1 # 假设最开始的是k-1下标的退出
    for num in range(n, 0, -1):
        # num表示现有的人数，会随着进行减少；i表示开退出的编号
        i = (i + m-1) % num
        print(people.pop(i))

josephos_l(10, 2, 5)
```
得出结果为：`6 1 7 3 10 9 2 5 8 4`

该算法的复杂度是 $O(n^2)$。外出循环体执行n次；内层的pop操作也是需要线性的时间，它的复杂度是n。

## 基于循环单链表的解法

采用循环单链表来实现，顺序报数，可以认为是沿着结点的next引用一直向后，退出后，就删除该结点。

实现步骤：
1. 创建一个长度为n的链表，
2. 循环链表，并删除确定的结点。

```python
class Josephos(SingleCycleLinkList):
    def append(self, item):
        # 重写一下尾部添加的方法
        node = Node(item)
        if self.is_empty():
            self._head = node
            node.next = node
        else:
            node.next = self._head
            self._rear.next = node
        self._rear = node

    def turn(self, m):
        for i in range(m):
            self._rear = self._rear.next

    def pop(self):
        node = self._rear.next
        if self._rear.next == self._rear:
            self._rear = None
        else:
            self._rear.next = self._rear.next.next
        return node.elem

    def run(self, n, k, m):
        # 创建长度为n的链表
        for i in range(1, n+1):
            self.append(i)

        self.turn(k-1)
        while self._rear:
            self.turn(m-1)
            print(self.pop())
```

执行后返回的结果为：

```python
josephos_link = Josephos()
josephos_link.run(10, 2, 5)
```
结果为：`6 1 7 3 10 9 2 5 8 4`

它的复杂度可以分为两部分，创建链表的复杂度是$O(n)$，遍历解决问题的复杂度是$O(n*m)$。

