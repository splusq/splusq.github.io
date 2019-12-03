---
title: 'Optimizing Regular Expressions'
date: Wed, 16 Feb 2005 16:23:02 +0000
draft: false
tags: ['Uncategorized']
---

Optimizing regular expressions is an exceptionally complex field. A vast majority of the problems within this field is `NP` or `NP-COMPLETE`. Meaning that their solutions cannot be bound (time/space) under any definite polynomial.

All APIs (such as: .NET, PHP, ASP (classic VB really), JavaScript) that support regular expressions performs some form of **equivalence reduction** meaning it tries to reduce the given regular expression into a reduced YET equivalent regular expression. Of course there is no full-proof way of doing this for couple of reasons: (1) There is no way to guarantee that a reduced regular expression is the most efficient regular expression possible. At this moment there is no GENERAL way of proving that.

I am writing about this topic because I am in the process of learning ASP.NET and I was just testing some of it's regular expression capabilities so I tried (as you can see I wasn't thinking):

```javascript
([a-z]+)*!
```

And it took longer than I thought. As soon as I changed the regular expression to the equivalent regular expression:

```javascript
([a-z])*!
```

It ran much much faster. This is a special case of nesting the Kleene star (\*) which always takes precedence over the (+) operator. Most text-books actually don't even define the (+) operator because it can be simplied to the following:

```javascript
a+ = aa*
```or in our case```
[a-z]+ = [a-z][a-z]*
```If we take it a little futher we get:```
 ([a-z]+)*! 
= ([a-z][a-z]*)*!
= ([a-z]*[a-z]*)! 
= [a-z]*! (COMPLEX STEP) 
```

The last step I would assume is where .NET couldn't reduce any further. The last step although easy to see why they are equivalent it's hard to develop a general algorithm to detect that.