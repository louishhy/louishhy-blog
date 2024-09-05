---
title: A skim through "K&R" - The C Programming Language (2nd Ed.)
description: ""
date: 2024-09-05T14:10:27.723Z
preview: ""
draft: false
tags: [C]
categories: [Programming languages]
image: "C_Programming_Language.svg"
slug: a-skim-through-k-and-r
---
## 0. Introduction
I came across with a lot of recommendations on one shall start their life in computer science with C/C++. Now, as a guy that have been programming in Python with empty brain since my undergrad CS intro course, I am finding myself asymptotically tending to the state of a *diaobaoxia* 调包侠, a term in Chinese that means a person that has minimum CS knowledge and finishes all his work on package APIs.

Even though I am not yet a C programmer, the motivation of reading this 1988 book is to "pay respect" to this language that has been the foundation of many modern tools. In other sense:
1. What is the, somehow, "minimum building blocks" of a programming language?
2. Learn to appreciate the evolution of the programming languages and many tools that I have been taking for granted.

Do note that this read is a simple skim.

## 1. Variables, Functions and Scopes
### 1.1 Variables
That was some time ago, but someday I suddenly asked myself:

> **What is a variable?**

Although might be incorrect, currently I like to think of a variable as a synonym of a memory address, so that you do not need to memorize the hexadecimal itself. After all, down to the assembly, the system just fetch the data from the memory to the registers and write back (at a proper time).

### 1.2 Functions
Knowing "pass-by-what" is the most important when you learn a new language. 

I really like the simplicity of "pass-by-value" in C. Everything passes by value. The essence of modifying the values inside a function is actually passing a copy of the pointer, and you modify what the pointer is pointing to.

Also, I am quite amazed how early a function with **variable sized parameter** lists was introduced. C can actually support this using the `stdarg.h` library.

### 1.3 Scopes and encapsulation
C is not an OOP language. However, there are some concepts that shed lights on later languages. This is actually encapsulation in its youth. Check out those concepts:
- `static`
    - If defined out of blocks: Local to the file, not visible to other files.
    - If defined inside functions: Local to the func, the variable is not destroyed after the function is done.
- `const`
    - The variable is read-only.
- `extern`
    - A declaration without definition. The variable is defined in another file.

C provides encapsulation on the **file** level, and actually takes a strategy that is quite different from OOP languages today that those functions are visible to other files. To do encapsulation you have to use `static` explicitly. It makes sense if I imagine its way of merging all those files together into an executable...

### 1.4 Declarations and Definitions
- **Declaration**: Tells the compiler about the type of the variable or function. Does not allocate memory.
- **Definition**: Allocates memory for the variable or function.

Somehow it tells me what it means when I right-click in VSCode and it asks me whether I want to "Go to definition" or "Go to declaration". Within the context of C it is more than clear.

## 2. Flow of control
C supports `for`, `while`, `do-while`, `if-else`, `switch-case`, and `break` and `continue` statements. Pretty much the same like other modern languages.

[`goto` considered harmful? :)](https://homepages.cwi.nl/~storm/teaching/reader/Dijkstra68.pdf)

The only thing I would like to take note is its explanation on the for statement.

```c
for (initialization; condition; update) {
    // body
}
```

is equivalent to

```c
initialization;
while (condition) {
    // body
    update;
}
```

which clearly solves any confusion of mine on the C style `for` statement. Thanks. :)

## 3. "The consistency"
As an analogy I would say that other languages are like cloud services while C is like on-premises. It somehow exposes the "bare metal" to you, but in a __simplistic, consistent__ way.

- Strings are _arrays_ of chars terminated by `\0`.
- Array names are _pointers_ to the first element.
    - When passed to a function, it decays to a pointer.
- Functions are _pointers_ to the entry point.

Pointers does give you headaches, but it really burns everything nearly down to its core - many things are essentially the same.

## 4. Importing?

I do not really think much when I type:

```py
import os
```

C does the task of pulling the code from other files (or, code from others) using __preprocessor directives__. 

What it does is actually expands the code before the compilation.
- `#include`: Expands the code from the file.
- `#define`: Replaces the text with the defined text.

And, since those functions are visible to other files, a header would be fine to tell the compiler what functions to find. 

Do notice that in C, duplicated definitions are not allowed. You kind of appreciate how great the invention of namespaces are.

## 5. How far is `struct` from `class`?

C does not have classes, but it has `struct` which is a way to group variables together. I sometimes think that how far away is it from a class in OOP languages. 

After all, structs can store variables and even functions inside, and the way of accessing them (`obj.var1`) is quite similar to OOP languages.

```
#include <stdio.h>

// Function declarations
void greet() {
    printf("Hello!\n");
}

void farewell() {
    printf("Goodbye!\n");
}

// Define a struct with function pointers
struct Person {
    char *name;
    void (*greetFunc)();     // Function pointer for greeting
    void (*farewellFunc)();  // Function pointer for farewell
};

int main() {
    // Create an instance of Person and assign functions to function pointers
    struct Person p;
    p.name = "Alice";
    p.greetFunc = greet;       // Assign greet function
    p.farewellFunc = farewell; // Assign farewell function

    // Call the functions through the function pointers in the struct
    printf("%s says: ", p.name);
    p.greetFunc();

    printf("%s says: ", p.name);
    p.farewellFunc();

    return 0;
}
```

Nevertheless, this comparison is somehow meaningless, since structs are tend to be used for data storage. But it is intriguing to see the "sprout" of later languages.

## 6. Interact with the system
### 6.1 I/O
I/O in a broad sense can be regarded as a serial input and output, with system calls like `lseek` to provide random access. But nevertheless it is just like moving a pointer around in a file or to read byte-by-byte from a stream that you do not know where it ends. That's why `EOF` is important.

### 6.2. Exceptions not at home?

It is the first time I know that C does not have built-in exception handling. It is quite a surprise to someone who have been taking `try-except` for granted.

I remember that the instructor in the Intro to CS in CMU has stressed on "checking the return value of system calls". Now somehow I know the reasons better, since those kind of exceptions are reported by the return values. 

This is probably also why separating the `stdout` and `stderr`, and do not reporting errors using `print()` is a good practice, since people used to have to analyze the return values manually and report the errors to the console.

### 6.3 Prototypes of formatting and access control
Think of python formatted string:
    
```py
num = 5
print(f"Number is {num:9.3f}")
# Output: Number is     5.000
```

In C this is originated from the `printf` function, where after the `%` you can specify the width and precision of the output.

```c
%<width>.<precision><type>
// You can also specify 
// left-justified 
// with a `-` before the width
```

Also think of python's file access control:

```py
with open("file.txt", "r") as f:
    print(f.read())
```

In C, you can specify the mode of the file access in the `fopen` function. There are `r` (read), `w` (write), `a` (append), and `b` added to those modes to specify binary mode.

```c
FILE *f = fopen("file.txt", "r");
```

So, many things do have their roots in the history.


## 7. Epilogue
First, here is my very, very naive overview that I derived from the C language:
- Variables
- Functions
- Operators
- Flow of control: Conditionals and loops.
- Memory management
- Typing system
- Set types: Arrays, structs (those types that store multiple variables)
- Multiple files and compilation
- I/O

Many modern languages do stem from this template, so you get to somehow know the new languages by first looking into this template and know how they operate, then investigate what problems they try to solve using this new language.

I remember a post that said "every time you read K&R after some growth in programming, you will find something new".
I **did** find many things new in this first quick pass, especially when I have been using high level programming languages for some time. It is like a journey back to the roots and solved many of my confusions.
Just like artists have to learn the history of art, I start to think that understanding the low level languages,  touching the fundamentals and somehow looking in the past makes you gain some knowledge on where you are standing right now, and peek into the essence of the machine before you.