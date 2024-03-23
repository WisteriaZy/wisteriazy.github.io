---
title: 列表推导式（List Comprehension）
date: 2024-03-23 08:29:53 +0800
categories: [Python]
tags: [Python]
---

[文章链接](https://zhuanlan.zhihu.com/p/36849145#:~:text=%E4%BB%80%E4%B9%88%E6%98%AF%E5%88%97%E8%A1%A8%E6%8E%A8%E5%AF%BC%E5%BC%8F%EF%BC%9F%20%E5%88%97%E8%A1%A8%E6%8E%A8%E5%AF%BC%E5%BC%8F%E6%88%96%E8%80%85%E8%AF%B4%E5%88%97%E8%A1%A8%E8%A7%A3%E6%9E%90%E5%BC%8F%E6%98%AF%E4%B8%80%E7%A7%8D%E9%9D%9E%E5%B8%B8%E7%AE%80%E6%B4%81%E7%9A%84%E5%88%9B%E5%BB%BA%E5%88%97%E8%A1%A8%E7%9A%84%E6%96%B9%E5%BC%8F%E3%80%82,%E5%BE%88%E5%A4%9A%E6%97%B6%E5%80%99%E6%88%91%E4%BB%AC%E9%9C%80%E8%A6%81%E5%88%9B%E5%BB%BA%E4%B8%80%E4%B8%AA%E6%BB%A1%E8%B6%B3%E7%89%B9%E5%AE%9A%E8%A6%81%E6%B1%82%E7%9A%84%E6%96%B0%E5%88%97%E8%A1%A8%EF%BC%8C%E4%B8%8D%E5%BE%97%E4%B8%8D%E7%94%A8for%E5%BE%AA%E7%8E%AF%E6%9D%A5%E5%88%9B%E5%BB%BA%EF%BC%8C%E8%80%8C%E7%94%A8%E5%88%97%E8%A1%A8%E6%8E%A8%E5%AF%BC%E5%BC%8F%E6%9D%A5%E8%A1%A8%E8%BE%BE%E5%8F%AA%E9%9C%80%E8%A6%81%E4%B8%80%E8%A1%8C%E4%BB%A3%E7%A0%81%E5%8D%B3%E5%8F%AF%E3%80%82%20%E5%88%97%E8%A1%A8%E6%8E%A8%E5%AF%BC%E5%BC%8F%E5%8F%A6%E4%B8%80%E4%B8%AA%E4%BC%98%E7%82%B9%E6%98%AF%E7%9B%B8%E6%AF%94%E4%BA%8Efor%E5%BE%AA%E7%8E%AF%E6%9B%B4%E9%AB%98%E6%95%88%EF%BC%8C%E5%9B%A0%E4%B8%BA%E5%88%97%E8%A1%A8%E6%8E%A8%E5%AF%BC%E5%BC%8F%E5%9C%A8%E6%89%A7%E8%A1%8C%E6%97%B6%E8%B0%83%E7%94%A8%E7%9A%84%E6%98%AFPython%E7%9A%84%E5%BA%95%E5%B1%82C%E4%BB%A3%E7%A0%81%EF%BC%8C%E8%80%8Cfor%E5%BE%AA%E7%8E%AF%E5%88%99%E6%98%AF%E7%94%A8Python%E4%BB%A3%E7%A0%81%E6%9D%A5%E6%89%A7%E8%A1%8C%E3%80%82)

## 什么是列表推导式？

列表推导式或者说列表解析式是一种非常简洁的创建列表的方式。很多时候我们需要创建一个满足特定要求的新列表，不得不用for循环来创建，而用列表推导式来表达只需要一行代码即可。列表推导式另一个优点是相比于for循环更高效，因为列表推导式在执行时调用的是Python的底层C代码，而for循环则是用Python代码来执行。  
比如我们需要创建一个包含平方数的列表，用for循环实现方式如下：

```python
squares = []
for i in range(10):
    squares.append(i**2)

print(squares)
```

如果用列表推导式的话只需一行代码即可实现：

```python
squares = [i**2 for i in range(10)]
```

## 列表推导式语法

![](https://pic1.zhimg.com/v2-35f312ea9c5cc1bf5bf3e01cf53ecdec_b.jpg)

列表推导式的语法结构可以分为几部分：

1. “\[\]”,定义列表的中括号。
2. for循环初步定义列表。
3. 可选：在for循环后面可以使用if语句进行过滤。
4. 在for循环前定于列表的元素表达式，可以是任意的表达式。可以是for循环中的元素本身，也可以是元素进行运算后的结果，也可以是元素组成的元祖或者列表，可以是一个函数，甚至可以是另一个列表解析式（嵌套列表解析式）。
5. 可选：在for循环后面可以再嵌套for循环。

## 列表推导式常用方式

```python
>>> vec = [-4, -2, 0, 2, 4]
>>>
>>> # 新生成一个列表，该列表中的每个元素是vec列表中每个元素的2倍
>>> [x*2 for x in vec]
[-8, -4, 0, 4, 8]
>>>
>>> # 利用if语句对列表进行过滤，将小于0的元素过滤掉
>>> [x for x in vec if x >= 0]
[0, 2, 4]
>>>
>>> # 对列表中每个元素应用函数，比如使用abs()函数对每个元素取绝对值
>>> [abs(x) for x in vec]
[4, 2, 0, 2, 4]
>>>
>>> # 对列表中每个元素调用方法
>>> freshfruit = ['  banana', '  loganberry ', 'passion fruit  ']
>>> [weapon.strip() for weapon in freshfruit]
['banana', 'loganberry', 'passion fruit']
>>>
>>> # 创建一个元素由元祖组成的列表，形式为(数字x,数字x的平方)
>>> [(x, x**2) for x in range(6)]
[(0, 0), (1, 1), (2, 4), (3, 9), (4, 16), (5, 25)]
>>> # 创建一个元素由列表组成的列表，形式为[数字x,数字x的平方]
>>> [[x,x**2] for x in range(6)]
[[0, 0], [1, 1], [2, 4], [3, 9], [4, 16], [5, 25]]
>>>
>>> # 利用两个for循环将一个嵌套二维列表降为一维列表
>>> vec = [[1,2,3], [4,5,6], [7,8,9]]
>>> [num for elem in vec for num in elem]
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

## 嵌套列表推导式

在**列表推导式使用方式语法**第三条中已经说明了，for循环前面的元素可以是任意的表达式，那么自然也可以是一个列表推导式了。

## 嵌套列表推导式举例

问题：定义一个3X4的矩阵，想办法将这个矩阵反转为4X3的矩阵

```python
>>> matrix = [
...     [1, 2, 3, 4],
...     [5, 6, 7, 8],
...     [9, 10, 11, 12],
... ]
```

使用for循环解决：

```python
>>> transposed = []
>>> for i in range(4):
...     transposed.append([row[i] for row in matrix])
...
>>> transposed
[[1, 5, 9], [2, 6, 10], [3, 7, 11], [4, 8, 12]]
```

使用嵌套列表推导式：

```python
>>> [[row[i] for row in matrix] for i in range(4)]
[[1, 5, 9], [2, 6, 10], [3, 7, 11], [4, 8, 12]]
```

