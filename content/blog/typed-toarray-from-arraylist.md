---
title: 'Typed ToArray() from ArrayList'
date: Fri, 09 Sep 2005 07:47:12 +0000
draft: false
tags: ['C#', '.NET']
---

To get a typed Array of type int \[\] from an ArrayList you can do the following:

```csharp
(int [])TestArray.ToArray(typeof(int));
```

First the ToArray() function accepts a System.Type argument which is what it uses to convert the Array to type int. However, the return type of ToArray() is still object so we will need to cast that as well which can be done using the (int \[\]).