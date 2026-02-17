---
title: 'Kolmogorov Complexity'
date: Wed, 28 Apr 2004 16:29:01 +0000
draft: false
tags: ['Algorithms']
---

A computer science/mathematics student learns various ways of measuring complexity of a problem. I feel this is an extremely important field because it allows us to measure the difficulty-level (or complexity of a problem). Without it, it would be like trying to lift a box without knowing how much it weighs.

For instance, how complex is it to sort N numbers? Using standard notation it turns out the best possible solution can be: O(n\*lg(n)) (we can prove that this is the best possible solution). This is the so-called: Big-O notation; something a freshman/sophomore CS student learns.

Well last night we were learning this new kind of complexity measurement technique known as: Kolmogorov Complexity. The idea is very simple. Here's a definition of it (in my words):

**Kolmogorov complexity of an object O is the length of the shortest program written to describe O.**

That's it! That's all there is to it. A simple yet powerful statement. Let's just look at it into a bit more detail.

First of all what does the term: object refer to? It refers to anything that can be described by a computer.

Secondly doesn't the length of an algorithm depend on the language? I mean solving a problem using C++ might take 4 lines but translate to 30 lines of assembly language code. Which one do you choose? The answer is to work on a common base. Meaning solve the problem using a universal Turing Machine (which is a theoretical computer).

Finally how do you know you have found the shortest program? Maybe the proposed solution is not the shortest, how can we know for certain we have found the shortest program? The answer depends on the problem domain, and cannot be answered in general. However in most cases it can be proven that a solution has to be the shortest.

If you are interested in this topic you would love to read [this Journal](http://www3.oup.co.uk/computer_journal/hdb/Volume_42/Issue_04/) (available for FREE) at: [http://www3.oup.co.uk/computer\_journal/hdb/Volume\_42/Issue\_04/](http://www3.oup.co.uk/computer_journal/hdb/Volume_42/Issue_04/)