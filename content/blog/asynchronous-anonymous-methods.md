---
title: 'Asynchronous Anonymous Methods'
date: Wed, 27 Feb 2013 04:04:42 +0000
draft: false
tags: ['C#', '.NET']
---

So a cool new feature in .NET 4.5 is the ability to create anonymous methods that are asynchronous (async).

```csharp
 Task.Run(async () => await Task.Yield()); 
```

That's just a cool way of saying do nothing!