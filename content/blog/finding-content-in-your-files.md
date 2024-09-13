---
title: 'Finding Content in your files'
date: Sat, 17 Sep 2005 00:57:23 +0000
draft: false
tags: ['Uncategorized']
---

If you want to search text files in your computer for a specific string you can do this easily using the following code:

```bash
for /f "tokens=*" %i IN ('dir /s /b *.asp') DO find /I "depthomepages" %i >> output.txt 
```

You can replace dir /s /b \*.asp to something more appropriate. You can alternatively search only specific folders also, by simply modifying the dir arguments.