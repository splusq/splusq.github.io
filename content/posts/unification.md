---
title: 'Unification'
date: Fri, 02 Apr 2004 18:51:33 +0000
draft: false
tags: ['Uncategorized']
---

In PROLOG there is an idea called: unification. It's similar to setting a variable to something but there are certainly several difference (otherwise it would be called assignment). One major difference in unification is pattern matching. For instance if I had the following code segment in PROLOG

```javascript
X / Y = 10 / a
```

Then the variable X will be assigned 10 and the variable Y will be assigned a. This is very powerful tool. Unification however has some limitations. For instance, unification with bounds-check (the default unification in PROLOG) won't allow one to do the following.

```javascript
X is X+1
```

That's because the right hand side contains (or is an improper subset) of the left hand side. This unification with bounds check is computed using substitution which I will further discuss at some other time.

For now that was a bit of PROLOG for you!