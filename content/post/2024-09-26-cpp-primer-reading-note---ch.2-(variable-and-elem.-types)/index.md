---
title: CPP Primer Reading Note - Ch.2 (Variable and Elementary Types)
description: ""
date: 2024-09-26T06:28:54.743Z
preview: ""
draft: false
tags: [c++, c++ primer]
categories: [c++]
image: ""
slug: cpp-primer-reading-note-ch2
---
Chapter 2 mainly talks about variables and elementary types in C++.

Except for some knowledge that is common for most languages, here are some interesting things I would like to make notes of.

## Object
Although many conventions about the meaning of object exist, in C++:

> An object is **a region of memory** that can hold values of **a given type**. 

So "variables" can be regarded as "objects".

Understanding this can be extremely helpful for further studies.

## Reference and pointer
- A _reference_ is an **alias** of an object that must be initialized. A reference is **not an object**. 
- A _pointer_ is **an object** (indeed! It has a memory space, an address and has type.) that can point to another object. 

## Top-level const and low-level const
- Top level const: `const int a = 1;`
    - The `const` applies constant constraint to the object (top level).
    - So the value of a cannot be changed.
    - For pointers: `int *const ptr = ...`
        - The pointer itself is a constant, so the address it points to cannot be changed. But the value of the underlying object can be changed.
- Low level const: `const int *ptr = ...`
    - The `const` applies constant constraint to the object the pointer points to (low level).
    - So the value of the object cannot be changed through the pointer.
    - **IMPORTANT** but it doesn't mean the object itself is a constant. The object can be changed through other means. Imagine that the pointer or the reference "thinks" that the object is a constant, so it refuses to change the object through itself - but the underlying object could be changed through other ways.

## Right-to-left reading rule
- `const int *ptr`
    - Read from right to left: `ptr is a pointer to a constant integer`
- `int *const ptr`
    - Read from right to left: `ptr is a constant pointer to an integer`

## `constexpr` and `decltype`
- `constexpr` is a specifier that indicates that the value of the variable or function can be evaluated at compile time. 
- `decltype` is a specifier that indicates the type of the variable or expression.

## About headers
In C++, the header usually contains the declaration of consts, constexprs, and class definitions (because they should only be defined once). The constraints of defining only once can be achieved by using the `#ifndef` directive.

## Scope of const
`const` has the default scope of a file. 
If you want to use the const across multiple files, you should use the `extern` keyword both in the declaration and definition.