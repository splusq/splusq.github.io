---
title: 'Legacy code'
date: Thu, 17 May 2007 00:52:11 +0000
draft: false
tags: ['General']
---

I manage a good portion of legacy code, written by so called consultants, and I can't believe how many places I have seen code like this:

```javascript
bool flag;
if(x==y) {
	flag=true;
}
else {
	flag=false;
} 
```

Or a variation of this

```javascript
bool flag=false;
	
if(x==y) {
	flag=true;
} 
```

Although the latter is better, you can write an equivalent statement as follows:

```javascript
bool flag = (x==y);
```

How much easier is that to read? In my opinion a lot! I've seen arguments against that however, suggesting that it's hard to read and not "extensible". Meaning, if your if clause contains more than one action then my proposed statement wouldn't work and would need to be rewritten. First of all, how's that not readable? In fact, I feel it's more readable than the other statements mainly because in a single line you see that flag is true if x equals y.

If you have more than one action under your if clause then you can't use this method, but we are not talking about if clauses with more than one action, we are talking about if clauses with a single action; and if during your maintenance you realize you need to add a second action simply convert that statement to an expanded if, else clause and you are done!