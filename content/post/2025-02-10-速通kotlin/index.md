---
title: 速通Kotlin
description: ""
date: 2025-02-10T03:36:36.163Z
preview: ""
draft: false
tags: [kotlin]
categories: [programming language]
image: ""
slug: quick-skim-on-kotlin
---

工作需要学习一下Kotlin，写一篇速通笔记。

## 思路
首先再复习一下看K&R学到的编程语言思路。
- 变量声明，赋值，以及数据类型
- 函数定义
- Collections（集合类）以及迭代方法
- 操作符
- Flow-of-control
- 内存管理模型
- I/O
- 编译

## 1. 变量
Kotlin声明方式有两种, `var`（引用可变）与`val`（引用不可变）。推荐不可变。
这个可以类比js的`let`和`const`。

类型可以参考Java，基本没有变化。其声明方式类似Py或者TS。
```
val nullableString: String? = null
```

此外Kotlin支持空安全，syntax为类似`String?`

## 2. 函数的定义
```kotlin
fun <function-name>(<function-args>): <return-type> {
    // body
}
```

和js一样，kt的函数是一等公民，可以作为参数和返回值。

此外kt还有一种非常神奇的中缀函数，
可以将函数当一个operator来使用。

```kotlin
infix fun Int.plus(x: Int) = this + x
1 plus 2
```

Kotlin的Lambda比较有意思。
```kotlin
val function_variable = { <parameters> -> <body> }

eg.

val sum = { x: Int, y: Int -> x + y }
```

Lambda如果只有一个参数也可以用`it`隐式引用。

```kotlin
val square = { it: Int -> it * it }
// 或者
val square: (Int) -> Int = { it * it }

list.filter { it > 0 }  // 过滤正数
```

Lambda可以多行。最后一行为返回值。

```kotlin
val transformer = { x: Int ->
    if (x > 0) {
        println("Positive number")
        x * 2
    } else {
        println("Non-positive number")
        0
    }
}
```

## 3. Collections
Kotlin常用的聚合类有三种，
并且每一种都有Mutable和Immutable的Variant。

- List / MutableList：数组
- Map / MutableMap：k-v pair
- Set / MutableSet：无重复元素的集合

一样地可以迭代。
```kotlin
// 列表迭代
val list = listOf(1, 2, 3)
for (item in list) {
    println(item)
}

// Map迭代
val map = mapOf("a" to 1, "b" to 2)
for ((key, value) in map) {
    println("$key -> $value")
}
```

## 4. 特殊操作符
除了各个语言中经常出现的操作符外：

### 4.1 Elvis
代表的意思是“如果非空则前，如果空则后”。
```kotlin
val name: String? = null
// 如果name为null，使用"Unknown"
val displayName = name ?: "Unknown" 
```

### 4.2 区间
```kotlin
// 闭区间
for (i in 1..5)

// 开区间
for (i in 1 until 5)

// 步长
for (i in 1..10 step 2)

// 倒序迭代
for (i in 10 downTo 1 step 3)
```

## 5. Flow-of-control
并没有非常显著的差别。

最大的差别主要是when的语法
```kotlin
val num = 3
val result = when (num) {
    1 -> "One"
    2, 3, 4 -> "Few"  // 多个值匹配
    in 5..10 -> "Several"  // 范围匹配
    else -> "Many"
}
println(result)  // Few
```

## 6. 关于内存和底层
大部分和JVM类似。

---

目前总体看下来kt的感觉是更加舒适的语法糖和空安全为主。

之后会看看Kotlin Compose Multiplatform。