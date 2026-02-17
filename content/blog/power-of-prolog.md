---
title: 'Power of PROLOG'
date: Wed, 21 Apr 2004 18:01:35 +0000
draft: false
tags: ['Algorithms']
---

Here's yet another example of the power available to you in PROLOG. Let's consider this simultaneous equation:

```prolog
3x + 4y = 17
4x + 3y = 18 
```

In PROLOG I can find the values of X and Y just like this:

```prolog
compute(X,Y):-
        fd_domain([X,Y],0,1000),
        3*X + 4*Y #= 17,
        4*X + 3*Y #= 18. 
```

That almost seems like I am joking -- but I am really not. That 3 lines of code will find all solutions to X and Y. So I tried the following:

```prolog
?- compute(A,B).

A = 3
B = 2

yes 
```

I should note, the values of X and Y are between 0...1000 (integers). All Contraint Logic Problems (CLPs) require a finite domain. That's why they are sometimes referred to as: Finite Domain (FD) Problems. Since CLP is not standardized each compiler supports FDs differently. I am in this case using [GNU Prolog](http://pauillac.inria.fr/~diaz/gnu-prolog/).

More to come!