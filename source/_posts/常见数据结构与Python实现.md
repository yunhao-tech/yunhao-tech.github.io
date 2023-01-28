---
title: 常见数据结构与Python实现
tags:
  - 数据结构
  - Python
categories: Python 深耕
cover: 'https://s2.loli.net/2023/01/29/fuGN2YyVgRU67pc.jpg'
description: 你知道如何在 Python 中实现栈、堆、队列、图吗？
abbrlink: df714f20
date: 2023-01-28 20:38:33
---

学习Python之后，仿佛栈、堆、队列等数据结构都离我远去了，这是因为Python以其高效的 list, dict, set 很大程度上代替了它们。但还是有必要梳理一下这些数据结构，了解它们的基本概念，以及在 Python 中如何实现。

有一点必须说明，Python 内置的 list, dict, set 数据结构与红黑树等各有优劣。单纯地评价好坏没有意义，必须结合应用场景灵活选择，详见倒数第二节的讨论。

# （非）线性数据结构

线性数据结构是有序数据项的集合，其中每个数据项都有**唯一**的前驱和后继（除了第一个没有前驱、最后一个没有后继）。

不同线性结构的区别关键在于数据项增减的方式：有的只允许数据从一端添加；有的则允许数据量从两端或中间添加。
常见的线性结构：**栈（stack），队列（queue），链表（linked list）**。

非线性数据结构主要包括：**树（tree）和图（graph）**.

# 栈 stack

数据的加入和移除都只发生在同一端（栈顶），另一端叫做栈底。

栈的进出规则遵循：**后进先出**（Last in First out, **LIFO**），越晚入栈的越早出栈。生活中的例子：一摞书、网页的后退操作、Undo操作。

## Python 中的实现

Python 的内置库实现了栈这种数据结构：`queue.LifoQueue()`

```python
import queue
stack = queue.LifoQueue()

stack.put(5)             # 对应 Push 操作，把数据压入栈
stack.get()              # 对应 pop 操作，数据出栈
```

Ps: 当然也可以自己构建栈结构。一种思路是：利用 List，把 List 的末端当作栈顶，元素的进出只能通过末端，此时 push/pop 的复杂度都是 O(1)。当然也可以把首端当作栈顶，但这样 push/pop 的复杂度都是 O(n)。下面是实现的样例代码：

```Python
class myStack():
    def __init__(self):
        self.items = []
    def isEmpty(self):
        return self.items == []
    def push(self, item):
        self.items.append(item)
    def pop(self):
        return self.items.pop()
    def peek(self):
        # 查看栈顶的数据，并不取出
        return self.items[-1]
    def size(self):
        return len(self.items)
```

## 栈的应用：括号匹配

问题描述：括号的使用要平衡，每个开括号要恰好对应一个闭括号；每对开闭括号要正确嵌套。识别括号是否匹配，是编程语言编译器的基础算法。

注意到，最早的开括号应该匹配最后的闭括号，这种次序反转正好符合栈 LIFO 的特点。具体来说，从左到右扫描，遇到左括号就压入栈；遇到右括号就取出栈顶的元素：如果是左括号，继续扫描；如果是右括号，直接返回 False。扫描完毕后，如果栈内还有元素，返回False，否则返回 True

对于小括号、中括号、大括号混杂的情况，思路也是一样。遇到闭括号时，取出栈顶元素，判断是否是同一类型开括号即可。样例代码如下：

```Python
def matches(open, close):
############### 通过比较下标来判断是否为同一类型的括号，思路值得学习 ###############
    opens = '([{'
    closes = ')]}'
    return opens.index(open) == closes.index(close)

def parChecker(symbolString):
    s = queue.LifoQueue()
    balanced = True
    for symbol in symbolString:
        if symbol in '([{':
            s.put(symbol)
        elif s.empty():
            return False
        else:
            top = s.get()
            if not matches(top, symbol):
                balanced = False
    return (balanced and s.empty())
```

---

# **队列 Queue**

新数据的添加总发生在一端（尾端），而数据的移除总发生在另一端（首端）。队列遵循**先进先出**原则（First in first out, **FIFO**)。生活中的例子：排队、KTV点歌

## Python 中的实现

**`queue.Queue()`**: API 详见 [queue — A synchronized queue class](https://docs.python.org/3/library/queue.html)

它的 `put` 和 `get` 方法都可以阻塞，因此它可以用于多线程之间、线程安全的通信。参考视频：[Python爬虫——生产者、消费者之间的多线程通信](https://www.bilibili.com/video/BV1bK411A7tV/?p=5)

> put: block if necessary until a free slot is available.
>
> get: block if necessary until an item is available.

---

# **优先队列 PriorityQueue**

优先队列与队列的区别在于：数据存在优先级，优先级高的数据会先出。如果优先队列中**数据的（键）值越小，优先级越高**，就称之为**升序优先队列**，反之称为降序优先队列。这里我们考虑升序优先队列。生活中的例子：排队时商家总是先给VIP提供服务，因为他们的优先级更高。

对于优先队列的实现，就不得不提到完全二叉树和二叉堆。

完全二叉树：叶节点只出现在最底层和次底层，且最底层的叶节点都集中在最左边；每个非叶子节点都有两个子节点，最多只有一个例外。

节点3就是唯一的例外，非叶节点的它只有一个子节点

二叉堆（**Heap**）是一种特殊的完全二叉树，它额外拥有堆的性质：**子节点的值一定比父节点大**。这里默认指最小堆（根节点是值最小的节点）。

向二叉堆中插入元素：把待插入元素放在最底层最左边的非空位置，然后冒泡上浮，直到满足堆的性质；删除二叉堆中最小的元素：删除根节点，把最底层最右边的非空位置节点放在根节点，然后冒泡下沉，直到满足堆的性质。以上两个操作的时间复杂度都是 `O(log(n))`

一般采用二叉堆来实现优先队列。新的数据加入优先队列对应着向二叉堆中插入元素；（优先级最高的）数据离开优先队列对应着删除二叉堆中最小的元素。但有一点要注意，优先队列和二叉堆是在概念上完全不同的东西。

## Python 中的实现

Python 优先队列的底层实现也是二叉堆：`queue.PriorityQueue()`

# 二叉搜索树 BST

二叉搜索树（Binary Search Tree，BST）是一棵二叉树（每个节点最多有两个子节点）。它的目的是要实现数据快速的增删查改。

它要满足的性质：**左子节点的值比父节点的值要小，右节点的值要比父节点的值大**。在理想的情况下（BST 是平衡的），它增删查改的时间复杂度为 `O(logN)`；最坏的情况下，BST 退化为链表，时间复杂度为 `O(N)`。

{% img https://s2.loli.net/2023/01/29/17ZjQFdEgoSwmut.png %}

数据插入的顺序会直接影响 BST 的平衡程度，不同的插入顺序会导致树的高度不一样，影响树的查找效率。

> 基于这个问题，平衡二叉搜索树（Balanced BST）产生了。平衡树在插入和删除的时候，会通过旋转操作将高度保持在 logN。其中两款具有代表性的平衡树分别为AVL树和红黑树。AVL树追求全局平衡，导致插入和删除性能差，在实际应用中不如追求局部平衡的红黑树（Red-Black Tree，RBTree）。Java 中的 TreeMap 和TreeSet，C++ STL的map、multimap、multiset等，都用到了 RBTree 取代过长的链表。
> 

**红黑树的查找、插入、删除复杂度均可以保证 `O(log n)` 的时间复杂度**。至于红黑树的工作原理，[30张图带你彻底理解红黑树](https://www.jianshu.com/p/e136ec79235c) 这一篇讲得很清晰。

## Python 中的实现

Python 官方内置库并没有实现树一类的结构，如二叉搜索树、AVL树、红黑树等。网络上有一些讨论过原因，见：[Built-in binary search tree in Python?](https://stackoverflow.com/questions/17857496/built-in-binary-search-tree-in-python)

但有不少第三方库高效地实现了类似的数据结构。`sortedcontainers` 就是一个不错的库。它实现了 Sorted List, Sorted Dict, Sorted Set 三种有序数据结构（增删改查的时间复杂度也是 O(log n) 级别），并且在LeetCode下可以直接使用。在个人开发环境中，通过 `pip install sortedcontainers` 安装即可。

API 可以参考：[Python Sorted Containers](https://grantjenks.com/docs/sortedcontainers/)

# Python 数据结构的性能

## list

官方解释器CPython中，`list` 是通过C语言里的**可变长度数组**实现的。这个数组存储的是该 `list` 中每个元素的引用。数组中的数据在内存空间中是连续的，所以访问 `list` 中的某个下标所需时间与 `list` 长度无关，与下标大小也无关。

{% img https://s2.loli.net/2023/01/29/Ex8jyHGOYCl9mcN.png %}

上图是官方网站 [TimeComplexity - Python Wiki](https://wiki.python.org/moin/TimeComplexity) 给出的 `list` 各种操作的时间复杂度。可以看到，在末尾增删数据时，时间复杂度为常数；但一般的增删操作平均需要 `O(n)` 的复杂度；检查是否包含某个数据，也要 `O(n)` 的复杂度； 但修改某下标处的数据只需要常数的时间复杂度。

正如前面所说，红黑树的查找、插入、删除复杂度均可以保证 O(log n) 的时间复杂度**。**由此可见，`list` 与红黑树各有优劣。具体选择哪种数据结构，还是要看增删改查哪些操作用得更多一些。

## dictionary

CPython中，`dictionary` 是通过C语言里的**可变长度哈希表**实现的。如果字典里的 key 的哈希值各不相同，那么访问字典中某个 key 的 value 所花费时间是常数，不取决于字典大小。

{% img https://s2.loli.net/2023/01/29/YWwZmHtVyB64NoC.png %}

> The Average Case above assume that the hash function for the objects is sufficiently robust to make collisions uncommon.
> 

以上的平均时间复杂度基于一个假设：哈希碰撞并不常见。否则，改、查等操作的复杂度会更高，取决于哈希碰撞的频率。

---

# 图 Graph

图是另外一种非线性数据结构，它由节点和边组成。图是一个非常大的话题，光分类就有很多种：有向图与无向图、带权重图与无权重图、有环图与无环图等等。这里不详细展开了。

图在生活中随处可见：交通网络、互联网、社交网络的关系网等都可以方便地用图来表示。一些最短路径的算法，如经典的 Dijkstra 算法、A* 算法等都是基于图的算法。还有一些基于图的神经网络，如 GCN（Graph Convolutional Network）、GAT（graph attention network）等。

Python 中 `networkx` 是一个优秀的开源第三方库，为图的生成、可视化、修改提供了强大的API。