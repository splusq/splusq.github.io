---
title: 'Recursion in SQL'
date: Sun, 11 Sep 2005 15:21:31 +0000
draft: false
tags: ['Uncategorized']
---

Oracle database supports a very strange construct called: start with, and connect by. For instance if you have a table which has two columns one that defines the parent of the other column such as:

```bash
 Parent	Child
  0 		1
  1 		9
  9 		7
  7 		2 
```

The parent of 2 is 7 and 7's parent is nine and 9's parent is one and so on until finally 1 who parent is 0 signifying no parent. To find the root or the top-level parent given an ID cannot be done easily without recursion and SQL server does not support recursion in SQL contructs. But Oracle does, here's how we can solve this problem in Oracle:

```sql
SELECT *
	FROM test_connect_by 
START WITH parent = 0
CONNECT BY prior child = parent; 
```

So in the meantime I am solving this problem by recursing through a C# function. I would like to get away from that and rewrite the C# recursion into a SQL stored-procedure. I will post that solution as soon as I am ready.