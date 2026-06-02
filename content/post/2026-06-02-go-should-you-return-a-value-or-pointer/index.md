---
title: "Go: Should You Return a Value or Pointer for Structs?"
description: ""
date: 2026-06-02T11:59:11.917Z
preview: ""
draft: false
tags: ['go']
categories: ['programming language']
image: ""
slug: go-should-you-return-a-value-or-pointer-for-structs
---

Consider the following Go code,
which one would you prefer?

```go
type SomeStruct struct {
    // ... fields
}

// Choice 1
func NewSomeStruct() SomeStruct {
    return SomeStruct{
        // ... fields
    }
}

// Choice 2
func NewSomeStruct() *SomeStruct {
    return &SomeStruct {
        // ... fields
    }
}
```

## Why
I have been learning Go recently and, as a heavy user in other more high-level programming languages like Python, 
I find it quite intriguing to see Go throwing around pointers everywhere.

For those from the C/Cpp background it might seem not that interesting though.

The question I wanted to ask is, when returning structs, whether we should prefer or default to any one of those:
1. Return pointers
2. Return values
so to somehow lower the mental burden.

## Searching for answer

Unfortunately up till now I haven't seen official guidance on this topic. 
There are official discussions about [receiver types](https://go.dev/wiki/CodeReviewComments#receiver-type) (_When in doubt, use pointer receiver_) and
[function arguments](https://go.dev/wiki/CodeReviewComments#pass-values).

So, to consider whether to use values or pointers, we can take a look at the two sides:

**Using value**
- Copying is fast when structures are small.
- `slices`, `maps` are already pointer-ish, so no need to return by pointer. [Go data structure](https://research.swtch.com/godata)
- A value is more likely to live on the stack, which is good for performance.

**Using pointers**
- Faster, if the struct is large. 
    - Comes with caveats though, sometimes it will trigger heap allocations and garbage collection. Because you use pointers, they may be subject to escape analysis which escapes to the heap, causing GC pressures and performance issues.
- Some concurrency-related structures like `sync.Mutex` are NOT MEANT TO BE COPIED.
- You may need the `Optional` semantic, in that case pointers gives you the ability to return `nil`

## My rule-of-thumb: Identity
I decide to stick to the heuristics that I derived, in my **current stage**:
1. If the returned value does not have **identity** and is not too big, return values.
2. Else, default to using pointers.

If performance proves that pointers are a problem, then do the optimization.

What I say identity is that, for example:
```go
type Point struct {
    X, Y int
}
```
then, semantically,
```go
Point{1, 2} == Point{1, 2}
```

Those two points are "identity-less", they basically represent the same thing.

But, if we are talking about
```go
type DBConnection struct {
    socket net.Conn
}
```

then, each of those structs **should** represent different resources, and here pointer semantics wins.

You can think of why mutex locks should not be copied, since one mutex lock should relate to only one resource. 
A copied mutex would create two lock states that appear to guard the same thing but are actually independent.

