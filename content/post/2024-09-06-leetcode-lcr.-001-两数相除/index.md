---
title: Leetcode LCR. 001 两数相除
description: ""
date: 2024-09-06T02:34:34.101Z
preview: ""
draft: false
tags: [leetcode]
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

## 二分查找的变式
首先对于菜菜的我第一步二分查找我就没想到，不如说看着这个题目第一眼是摸不着头脑的。

既然我们谈到了二分，那么我们先复习一下吧。一个通用的二分查找模型将一个搜索范围划分为三个部分，其中`[0, bisect_left)` 为小于目标值的部分，`[bisect_left, bisect_right)` 为等于目标值的部分，`[bisect_right, END)` 为大于目标值的部分。

现在我们需要的是魔改一下判断的标准为 `mid * b` 与 `a` 的大小关系。我们要求的正是“小于等于目标值的范围的最大”，也就是`bisect_right - 1`。由于除法可以拿到负数，所以我们需要用a和b的绝对值来处理，最后返回的时候，根据 `a` 和 `b` 的符号来判断结果的符号。

我们先把overflow放到一边，乘号的限制也放到一边，写一个非常naive的解。然而，请注意二分查找的模板。

```py
import math

class Solution:
    def divide(self, a: int, b: int) -> int:
        INT_MAX = 2**31 - 1
        INT_MIN = -2**31

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

## 快速幂/快速乘
前面的二分查找需要用到乘法。有什么办法可以不用乘法呢？

先介绍一下简单的快速幂。快速幂是一种减少乘法次数的方法，它的核心思想是将指数`n`拆分为二进制的形式，然后利用二进制的性质来减少乘法次数。

例子：$2^{10} = 2^8 * 2^2 = 256$

我们并不需要将2连续乘10次。我们可以先算出$2^2$，然后再算出$2^4$，再算出$2^8$，最后再乘上$2^2$。

这个算法有一个对应的二进制的表达形式。$10 = 1010_2$。我们可以看到第3位和第1位是1，所以我们只需要计算$2^{2^1}$和$2^{2^3}$，然后相乘即可。

事实上，通过同样的方法，我们可以同时解决这题的“不用乘除法”的要求。
我们试想这么一个过程。

- `a = 19, b = 3`
- 计算b的偶数次倍数，直到其是小于等于a的最大值。`3, 6, 12`
    - 可以将他们视为：$3 * 2^0, 3 * 2^1, 3 * 2^2$
- 逆序遍历他们以分解a。
    - 19 - 12 = 7
    - 7 - 6 = 1
    - 1 已经小于3了，所以结束。
- 现在，a可以被表达为：$3 * 2^2 + 3 * 2^1 + \delta$
    - $\delta$是`a`除以`b`的余数，根据题意舍去。
- 它除以3的结果便是：$2^2 + 2^1 = 6$

注意到这种快速乘是不需要真正用到乘法的，数的翻倍可以通过自加来实现。

## Overflow问题
事实上我们目前还尚未解决溢出的问题。题目对于int的范围有所要求。用python没有正面的解决这些问题。

### 1. 范围不对称问题
这歌问题的第一个tricky点在于int范围并非对称，而我们的算法包含正负数。所以，第一步是将所有的数投射到负数区间（因为正数的最大值取负仍在范围内，反之则不然）。

### 2. 考虑特殊情况
我们考虑数的边界。注意到，我们全部的操作都是在负数区间进行。所以我们主要考虑`INT_MIN`的情况。
1. 如果被除数是`INT_MIN`，那么
    - 当它除以-1时会越界。此时我们应当返回`INT_MAX`。
    - 当它除以1时，`result`累加可能会越界。所以我们应当返回`INT_MIN`。
2. 如果除数是`INT_MIN`，那么
    - 如果被除数是`INT_MIN`，那么返回1。
    - 其他时候，应当返回0。

### 3. 累加的越界
注意到我们累加的时候很有可能在最后一步检查该数是否大于a的时候越界，所以，我们需要稍稍改变一下写法：
```
b + b <= a => b <= a - b
```
两个负数的差是不会越界的。

## 算法实现
leetcode平台的int恰好就是32位的。我们折磨一下自己，写一个C版本。
    
```c
#include <limits.h>

int divide(int a, int b){
    // In LC the int is 4-byte 32 bit int.
    if (a == 0)
        return 0;
    // Special cases
    if (a == INT_MIN){
        if (b == -1) {
            return INT_MAX;
        }
        else if (b == 1) {
            return INT_MIN;
        }
    }
    else if (b == INT_MIN) {
        if (a == INT_MIN)
            return 1;
        else
            return 0;
    }
    // Get sign
    int sgn_rev = 0;
    // Transform to negative
    if (a > 0) {
        a = -a;
        sgn_rev = !sgn_rev;
    }
    if (b > 0) {
        b = -b;
        sgn_rev = !sgn_rev;
    }
    // Define the b-array, has upper limit 32
    int b_arr[32];
    // Check if b_temp + b_temp < a
    int b_temp = b;
    b_arr[0] = b_temp;
    int b_arr_ptr = 1;
    while (b_temp >= a - b_temp) {
        b_temp += b_temp; // Will not overflow since the check is done in while
        b_arr[b_arr_ptr] = b_temp;
        b_arr_ptr++;
    }
    // Reverse back
    int result = 0;
    for (b_arr_ptr-=1; b_arr_ptr>=0; b_arr_ptr--) {
        int subtract_unit = b_arr[b_arr_ptr];
        if (a > subtract_unit)
            // Subtract unit too big
            continue;
        a -= subtract_unit;
        result += (1 << b_arr_ptr); // Shift for power
    }
    if (sgn_rev)
        result = -result;
    return result;
}
```

## 总结
记录这个题目主要是因为它复习到了很多的知识点：
- 它涉及到了一个二分查找的实现，帮助复习了二分。
    - 事实上，原题实现了一个基于快速乘法的二分查找。但是解释的不是非常直观，我选择了第二种用空间换时间的方法。
- 它涉及了快速乘和快速幂的基本原理和活用。
- 它涉及了overflow和overflow的处理。

这道“简单”题坑还是好多的。

>_下次还填非常简单。_
