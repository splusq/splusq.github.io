---
title: 'Automatic Documentation'
date: Thu, 31 May 2007 19:16:21 +0000
draft: false
tags: ['C#', 'Tools']
---

Generally all programmers are lazy--and specially when it comes to documentation because it takes so much time and doesn't necessarily produce any material benefit. [GhostDoc](http://www.roland-weigelt.de/ghostdoc/)1, designed by Roland Weigelt, is a plugin for Visual Studio 2005 that automates the generation of XML comments. For example if you have the following C#1 function:

```csharp
public User GetByUserName(string userName);
```

GhostDoc will automatically generate:

```csharp
 /// <summary>
/// Gets the name of the by user.
/// </summary>
/// <param name="userName">Name of the user.</param>
/// <returns></returns>
public User GetByUserName(string userName); 
```

That's pretty amazing. Notice how GhostDoc recognizes Camel and Pascal case variables names. And best of all once the basic structure of the comment is prepared it's actually not very difficult to type in few words that essentially sums up what the function does if GhostDoc hasn't already done it. Finally, since GhostDoc integrates directly with Visual Studio all you have to do is right click the function signature and click on: "Document this" and done! GhostDoc also supports: enums, getters and setters, inheritance, interfaces and so on... \[1\] [GhostDoc](http://www.roland-weigelt.de/ghostdoc/) currently only supports C#