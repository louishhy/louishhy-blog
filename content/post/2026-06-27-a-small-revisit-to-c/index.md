---
title: A small revisit to C
description: ""
date: 2026-06-27T14:26:09.324Z
preview: ""
draft: false
tags: [c]
categories: [programming language]
image: ""
slug: a-small-revisit-to-c
---


Recently I had a sudden urge to revisit C programming language.
The last time I have been using C was in my university's operating systems class, which was a few years ago and to be honest I have forgot most, if not all of it.

I tried to re-learn some basic C, then write some little programs (including an attempt to write a small Redis).
C is indeed painful because it lacks many of the modern ergonomics,
but I think it is still a fruitful experience.

Personally, I had 4 major takeaways from the revisit.
- Programs can be abstracted as bits + semantics.
- Memory management is half the heart for C.
- Understanding system calls as one of the primitives.
- Rethinking about error handling.

## Programs = bits + semantics + (instructions)
Theoretically, instructions are also bits. That's why I put it in parentheses.

C gives the most "raw" way for you to deal with generic objects.
On a personal instinct level, `void*` means _"this points to some bytes on the memory, but how to interpret it (as what type) is your responsibility"_.

`void *malloc(...)` suddenly made more sense to me, since it can be interpreted as allocating just a series of bytes for you, and how you shall use it is up to yourself. By putting it into different semantics, you can let it stand for different meanings. But in the end, it is just bytes.

Its also interesting to see arrays as a kind of ergonomics inside C.
While most programming languages have bound-check for arrays, the array syntax somehow like a syntax sugar in C,
since there is no bound check, and in most expression contexts an array expression decays to a pointer to its first element。
That's why when manipulating arrays, the size of the array is usually needed as a function argument.
```c
int do_something(char *arr, size_t arrsize);
```

## Memory management
### Stack, heap and so on
You indeed have to think about the lifecycle for objects when writing C.

One classic example is that variables that are local to the block have the lifetime same as the block, so you should not return a pointer to a function local variable (as it is inside the stack).

Also revisited the allocation and reallocation stuff on the heap.
It is hence also an interesting topic to try to reduce allocations on heap, so as to reduce the bookkeeping for allocator and also the burden of manually freeing allocated space. 

### Virtual memory
C gives many exposure to virtual memory stuff, especially the `mmap` function. It is interesting to see that you can perform I/O by mapping the file into the virtual memory space in the program, and do random access with that. Anonymous mapping of `mmap` is also an interesting way for allocating memory.

In a broader sense, reasoning about stack and heap and the object's lifecycle (such as: where `static` objects typically live at, why using dynamic linking and how are they mapped into virtual address space) is also a great lesson about virtual memory.

### Ownership models
One very interesting paradigm is that in C, results are often NOT returned directly, but written into a buffer (a prepared workspace) that the **caller** prepares.
```c
int do_something(const char *in, size_t insize, char *outbuf, size_t bufsize);
```
Instead of returning directly, the result is retrieved from the buffer.

This is because that in this model, the caller prepares the memory space for the output. Hence, it is natural that it's the caller's responsibility to free the resource. If the callee asked for heap allocation, it is possible that:
- In some environments, especially across library/runtime/allocator boundaries, freeing memory with a different allocator from the one that allocated it can be invalid. This is why many C APIs either let the caller provide the buffer or provide a matching destroy/free function.
- You need careful documentation that delegates the responsibility of freeing the memory to the caller.

Another common pattern is that the caller just uses a local variable on stack memory, and then pass the pointer to the callee. Hence, there is no need to manually free the memory.

You can see that the reasoning about object lifecycle is everywhere.

### Guarding for invalid access
As we have said, C usually does not have size information unless you explicitly implement one. That is why bound-checking is so important in C since you want to prevent the program access memory segments that does not belong to you. You usually want to explicitly give the size of the buffer, or how many bytes at MAX do you want to write in.

Recall that, after all all things are values and pointers to memory addresses when compiled to assembly.
It makes more sense to think it in the way of assembly, where there are just registers that either stores values, or registers that stores the memory address of values.
I think that's why many people say that C is close to assembly.

## System internals
There are also a lot of stuff I learnt from using system calls or wrappers for system calls.
- I/O: `read()` and `write()` taught me how to do I/O by just viewing them as a stream of bytes, and that the OS may not obey your instruction all the time and you have to pick up where the stream is left off.
- Virtual memory: `mmap` and other memory allocation stuff let me know about the virtual memory.
- Network: `socket` etc. made me understood how to get informations from the abstraction, and how to properly parse a TCP stream.
- Files: Reviewed about file descriptors, how they relate to open file description and `inode`, and how `dup2` / `pipe` can do redirections and piping. Also, how to interpret the file access as stateful and using `lseek` to adjust the offsets.

C is very close to the OS internals and I think this is like "half-of-C".

## Error handling
C does not have throwing exception mechanisms, so all errors are checked by checking the return values.
I think Go's error mechanism finds its root (?) in C, because in C you write something very similar to `if (err != nil)` stuff, like checking the syscall's return values or the return values for string manipulations that can fail. Go has better ergonomics though, as functions can have multiple return values.

There is a constant argument about whether error handling should have a separate route (like the throwing exception stuff in Java) or it has to be checked at every level like C / Go.
My opinion is that:
- For complicated, domain-dependent software like enterprise software stuff (which is usually spaghetti code in production, because the intrinsic complexity of componded domain logic), it is better to have a stacktrace error mechanism that drills from the leaf node to the top.
- C / Go like style is more or less better for those software that are low on business logic but have its complication lying in algorithms, like database internals or infra toolings.

## Ending
At least for now I am not going to be a kernel engineer anytime soon.
But I think this revisit is fruitful for me as a computer science "student" as it reveals many of the logic down below.
It forced me to think about lifecycles of objects inside memories, and reason about how to release resources timely to prevent leaks.
For the next step in my free time I will probably touch some Rust to see how it attempts to solve some of the mistakes that is easy to make in C.