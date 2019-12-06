---
title: 'Bit Operations'
date: Wed, 10 Mar 2004 23:30:07 +0000
draft: false
tags: ['Uncategorized']
---

A friend of mine once asked me what the following code does.

```c
register int a;
a ^= a; 
```

After a bit of thought I realized that "a" gets initialized to zero. But why do it that way when you can do it like this:

```c
int a;
a = 0; 
```

The answer lies in the machine translation for each of these segments. Let's look at it one piece at a time. The first code segment requests "a" to be part of one of the several general paramenter registers in the CPU. If that's granted then the statement a^=a; is simple XOR edx, eax.

Compared to the second code: Which moves 0 to a register - 1 instruction does several (I would think around 2) `pushl` 's whereas the first one does `XOR` ...,... directly.

I compiled the above two programs GCC reported a net savings of 9 bytes. That's just with one use of this technique other uses will give a greater net-savings if used properly.

Without a doubt this will affect maintenance and readibility of the code; and perhaps shouldn't be used on production systems. But when you need to squeeze that little extra bit out so that everything fits on one frame -- you have to do what you have to do!