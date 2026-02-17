---
title: 'Constraint Logic Programming'
date: Wed, 21 Apr 2004 06:07:51 +0000
draft: false
tags: ['Algorithms']
---

PROLOG can is one of many languages that can be used to perform Constraint Logic Programming (CLP). Just like C++ and Visual Basic can be used to program Object Oriented Programs (OOP). Same idea.

The idea behind CLP is the following: give contraints to a problem and request all solutions. For instance, if I had:

```prolog
X+Y=100
```

Suppose I wanted to find all the integer solutions to this problem it would be very difficult to code in a regular language (not so much in this specific case because it's too simple, but in general). Because the programmer would have to manually control backtracking and assignment and all sorts of stuff. However take a look at this PROLOG program:

```prolog
sum(Total) :-
        print('Solution: '), nl,
        member(X,[0,1,2,3,4,5,6,7,8,9,10]),
        member(Y,[0,1,2,3,4,5,6,7,8,9,10]),

        K is X+Y,
        K is Total,
        print('     X= '), print(X), nl,
        print('     Y= '), print(Y), nl,nl,fail. 
```

Given a total this programs finds all the possible solutions (this sample program assumes Total<=20). Just like that!! Isn't that something. I tried it with the following input:

```prolog
?- sum(7).

Solution:
     X= 0
     Y= 7

     X= 1
     Y= 6

     X= 2
     Y= 5

     X= 3
     Y= 4

     X= 4
     Y= 3

     X= 5
     Y= 2

     X= 6
     Y= 1

     X= 7
     Y= 0 
```

Fun stuff !!

I could have placed one further constraint: maximize X and Y. This is very useful in Game Theory (find the best solution), Chemistry, Biology and Physics (what maximum values satify a equation). And believe it or not, Economics.