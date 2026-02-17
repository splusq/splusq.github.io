---
title: 'Four Part Naming Convention in SQL Server'
date: Tue, 20 Sep 2005 05:41:16 +0000
draft: false
tags: ['Database']
---

In SQL server if you want to reference a table using four-part naming convention and you receive the error message: **Server 'xxxx' is not configured for DATA ACCESS**. Then you need to execute this statement against the target server:

```sql
EXEC sp_serveroption 'xxxx','DATA ACCESS',TRUE 
```

... that should fix the problem. An obvious point here is to replace xxxx with the target server.