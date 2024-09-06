---
title: Leetcode LCR. 001 两数相除
description: ""
date: 2024-09-06T02:34:34.101Z
preview: ""
draft: false
tags: []
categories: [Algorithm, 中文post]
image: "https://images.unsplash.com/photo-1535478044878-3ed83d5456ef?q=80&w=2691&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"
slug: leetcode-lcr.-001-两数相除
---

> **给他们一点小小的简单题震撼！**

## 题目
给定两个整数 a 和 b ，求它们的除法的商 a/b ，要求不得使用乘号 '*'、除号 '/' 以及求余符号 '%' 。


注意：

整数除法的结果应当截去（truncate）其小数部分。

例如：truncate(8.345) = 8 以及 truncate(-2.7335) = -2

假设我们的环境只能存储 32 位有符号整数，其数值范围是 $[−2^{31}, 2^{31}−1]$。

本题中，如果除法结果溢出，则返回 $2^{31} − 1$
 

示例 1：
```
输入：a = 15, b = 2
输出：7
解释：15/2 = truncate(7.5) = 7
```

示例 2：
```
输入：a = 7, b = -3
输出：-2
解释：7/-3 = truncate(-2.33333..) = -2
```

示例 3：
```
输入：a = 0, b = 1
输出：0
```
示例 4：
```
输入：a = 1, b = 1
输出：1
```

## 二分查找的自我理解
首先对于菜菜的我第一步二分查找我就没想到，不如说看着这个题目第一眼是摸不着头脑的。

既然我们谈到了二分，那么我们先复习一下吧。一个通用的二分查找模型将一个搜索范围划分为三个部分，其中`[0, bisect_left)` 为小于目标值的部分，`[bisect_left, bisect_right)` 为等于目标值的部分，`[bisect_right, END)` 为大于目标值的部分。

现在我们需要的是魔改一下判断的标准为 `mid * b` 与 `a` 的大小关系。我们要求的正是“小于等于目标值的范围的最大”，也就是`bisect_right - 1`。由于除法可以拿到负数，所以我们需要用a和b的绝对值来处理，最后返回的时候，根据 `a` 和 `b` 的符号来判断结果的符号。

我们先把overflow放到一边，乘号的限制也放到一边，写一个非常naive的解。然而，请注意二分查找的模板。

```py
import math

class Solution:
    def divide(self, a: int, b: int) -> int:
        INT_MAX = int(math.pow(2, 31) - 1)
        INT_MIN = int(-math.pow(2, 31))

        def sgn(num):
            if num == 0:
                return 0
            elif num > 0:
                return 1
            else:
                return -1
        
        def clamp(num):
            if num > INT_MAX or num < INT_MIN:
                return INT_MAX
            return num
        
        # ===== 这一部分是主要的二分查找部分 =====
        left, right = 0, abs(a) + 1
        # Get bisect_right
        
        while left < right:
            mid = left + ((right - left) >> 1)
            if mid * abs(b) > abs(a):
                right = mid
            else:
                left = mid + 1
        # ========================================
        result = left - 1
        return clamp(result * sgn(a * b))
```

二分查找的伪代码可以总结为：

```py
# 采用Dijkstra的左闭右开 [left, right)
while 仍有搜索空间 (left < right)：
    # 这个表达式在偶数元素时取得右中点。
    取得中点 mid = left + (right - left) // 2
    if (mid满足分割点以及分割点以右应当满足的条件)：
        right = mid
    else：
        left = mid + 1
    return left
```

以以上的题解作为例子。我们需要找到的是`bisect_right`。思考`bisect_right`的性质，[bisect_right, END) 为大于目标值的部分。所以，_分割点以及分割点以右应当满足的条件_ 为 `num * abs(b) > abs(a)`。

在逻辑上有一个相当反直觉的地方在于，`=target`的时候，为什么会把一个左闭右开的区间的`right`放在上面。这样岂不是否定了`mid`本身就为边界的可能？

我的理解是这是因为loop的退出条件是`left < right`， 并且return值为`left`。如若`mid`本身真的为边界，那么`left`最终将会等于`right`，此时它会重新回到`mid`上。这与数学直觉并不相符，因为`[a, a+1)`本身就说明这个整数为a，然而程序的退出点却是`[a, a)`。你也可以将其视为“程序应当检视的空间”，因为程序已然知道`mid`是可能解之一，所以自然地把它排除至需要检视的区域外。

我们来设想一个如果不这样做会导致的死循环。

```py
bisect_left(arr, 6)
arr = [..., 5, 6, 7, ...]
            ^  ^  ^
            |  |  |
            l mid r
```

如果我们要尝试寻找`bisect_left`，且update rule是`right = mid + 1`，也就是将mid继续放在搜索空间中时，这个程序会陷入死循环：它会一直把`r`放在`7`上。（回忆算法：`if (mid >= target) 则压缩右侧空间`。）

(WIP To be continued)