---
title: 2024年创意编程与智能设计大赛Python题解
date: 2024-03-30 16:41:14 +0800
categories: [Python,算法]
tags: [Python,算法]
---

## 1.罗马数字转整数

> 本题原题来自[力扣(Leetcode)](https://leetcode.cn/problems/roman-to-integer/description/)

罗马数字包含以下七种字符: I， V， X， L，C，D 和 M。

| 字符 | 数值 |
| --- | --- |
| I | 1 |
| V | 5 |
| X | 10 |
| L | 50 |
| C | 100 |
| D | 500 |
| M | 1000 |

例如， 罗马数字 2 写做 II ，即为两个并列的 1 。12 写做 XII ，即为 X + II 。 27 写做  XXVII, 即为 XX + V + II 。

通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 IIII，而是 IV。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 IX。这个特殊的规则只适用于以下情况：

I 可以放在 V (5) 和 X (10) 的左边，来表示 4 和 9。

X 可以放在 L (50) 和 C (100) 的左边，来表示 40 和 90。

C 可以放在 D (500) 和 M (1000) 的左边，来表示 400 和 900。

给定一个罗马数字，将其转换成整数。

**示例 1:**

输入: `s = "III"`

输出: `3`

**示例 2:**

输入: `s = "IV"`

输出: `4`

**示例 3:**

输入: `s = "IX"`

输出: `9`

**示例 4:**

输入: `s = "LVIII"`

输出: `58`

解释: *L = 50, V= 5, III = 3.*

**示例 5:**

输入: `s = "MCMXCIV"`

输出: `1994`

解释: *M = 1000, CM = 900, XC = 90, IV = 4.*

### 我的思路

我还不会QAQ

### 解法

> 解法来自[CSDN-求兵](https://blog.csdn.net/qiubingcsdn/article/details/81781977)

#### 暴力破解

```python
def romanToInt(s):
        result = 0
        temp_list = []
        for i in s:
            if i == 'I':
                temp_list.append(1)
            elif i == 'V':
                temp_list.append(5)
            elif i == 'X':
                temp_list.append(10)
            elif i == 'L':
                temp_list.append(50)
            elif i == 'C':
                temp_list.append(100)
            elif i == 'D':
                temp_list.append(500)
            elif i == 'M':
                temp_list.append(1000)
        for j in range(len(temp_list)):
            if j == len(temp_list)-1:
                result += temp_list[j]
            else:
                if temp_list[j]<temp_list[j+1]:
                    result -= temp_list[j]
                else:
                    result += temp_list[j]
        return result
print(romanToInt(input()))
```

#### 使用字典

```python
def romanToInt(s):
        result = 0
        temp_dict = {"I":1,"V":5,"X":10,"L":50,"C":100,"D":500,"M":1000}
        for i in range(len(s)-1):
            if temp_dict[s[i]]<temp_dict[s[i+1]]:
                result -= temp_dict[s[i]]
            else:
                result += temp_dict[s[i]]
        result += temp_dict[s[-1]]
        return result
print(romanToInt(input()))
```

---

## 2.加密

**输入:**

一个字符串,用空格分隔待加密的内容和一个包含4个数字的密钥

**输出:**

将待加密的内容按照4个一组,每组按照密钥的顺序加密,输出结果

**输入样例**

`czsshkzx 1324`

**输出样例**

`cszshzkx`

### 我的思路

首先获取并拆分输入的内容

循环字符串按照每4个一组装进列表,结果装进另一个列表

初始化结果列表(用于输出),遍历大列表,再遍历给的顺序

按照给的顺序获取列表中的数据并添加到新列表中,再把新列表装进结果列表中

最后输出结果

### 我的解法

```python
cmd = input().split()
count = 0
temp_list = []
result_list = []
for i in cmd[0]:
    if count == 4:
        result_list.append(temp_list)
        temp_list = [i]
        count = 1
    else:
        temp_list.append(i)
        count += 1
result_list.append(temp_list)

print_list = []
temp_list = []
for list in result_list:
    for i in cmd[1]:
        temp_list.append(list[int(i) - 1])
    print_list.append(temp_list)
    temp_list = []

# 输出结果,这里由于结果列表中还有列表,所以我是用了列表推导式
# 遍历结果列表再遍历小列表并将所有内容组合成一个临时的新列表
# 并以""(也就是没有空格)组合列表并输出
print("".join([n for i in print_list for n in i]))
```

---

## 3.排队

学生们正在排队,若排两列,则多出一个,若排3列,或者4,5,6列都多出一个,排7列正好

求最少几个人

### 我的思路

循环累加并按照题目要求判断即可

### 我的解法

```python
print("301") # 直接输出结果:P
```

**过程**

```python
n = 0
while True:
    if n % 2 == 1 and n % 3 == 1 and n % 4 == 1 and n % 5 == 1 and n % 6 == 1 and n % 7 == 0:
        break
    n += 1
print(n)
```
