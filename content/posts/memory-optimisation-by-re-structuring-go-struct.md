---
title: Memory optimisation by re structuring GO struct
date: 2021-12-22 13:42:00
tags:
    - go
categories:
    - tech
keywords:
    - go
    - memory optimization
---


### Identifying the problem(may be this is not right to call it problem, this is standard behaviour of how cpu accesses memory):

The CPU accesses memory by a single memory word at a time. As long as the memory word size is at least as large as the largest primitive data type supported by the computer, aligned accesses will always access a single memory word.

When the highest and lowest bytes of the datum are not in the same memory word the processor needs to access the data by doing multiple operations. This requires the processor to do many complex operations to generate memory accesses and coordinate them. 

### What is padding:

Though the compiler or may be interpreter normally allocates individual data items on aligned boundaries, data structures often have members with different alignment requirements. To maintain proper alignment the translator normally inserts additional unnamed data members so that each member is properly aligned. In addition, the data structure as a whole may be padded with a final unnamed member. This allows each member of an #struct to be properly aligned.

### Now we know padding now let's learn when padding is used:

Padding is only inserted when a structure member is followed by a member with a larger alignment requirement or at the end of the structure. By changing the ordering of members in a structure, it is possible to change the amount of padding required to maintain alignment. For example, if members are sorted by descending alignment requirements a minimal amount of padding is required. The minimal amount of padding required is always less than the largest alignment in the structure. 

**Going over your head?**

**Here are some examples that will make things easy:**

**Let's consider an example:**

```go
type A struct {
  aBool  bool    // 1 byte
  aFloat float64 // 8 bytes
  aInt   int32   // 4 bytes
}
```
 So if we create a instance of this struct:

```go
a := a{}
```
what do you think the memory size will be taken by a? 13?

Unfortunately, No.

The total size will be 24 bytes.

why?

To keep field's data in a #word every member of the structure is padded to fill up the 8 bytes of a word. For the first element aBool the actual data takes 1 byte and the other 7 bytes are padded.

so the memory taken is now 8. Then consider  aFloat as it takes 8 bytes no bytes need to be padded. so now the memory taken is 8+8 = 16 bytes. Now consider aInt, the actual data takes 4 bytes but another 4 bytes need to be padded. So the total memory consumed is now 8+8+8=24.

So how can we optimize this?

Let's look at the rearrangement.

```go
type A struct {
  aFloat float64 // 8 bytes
  aBool  bool    // 1 byte
  aInt   int32   // 4 bytes
}
```
It will now take just 16 bytes of memory. Phew! What an optimisation!

Let's explain:

**aFloat** is taking **8 bytes** and filling the word and no padding is required. aBool is required **1 byte** and another **7 bytes** is going to be padded but as aInt require only **4 byte** and it can be easily be in **7 bytes** so no other extra allocation will be happened for aInt. So the total memory allocation will be now **8+8=16** bytes. Note: An alignment can be 1, 2, 4, or 8.