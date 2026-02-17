---
title: 'Sequential Preloading..'
date: Fri, 25 Jun 2004 14:45:49 +0000
draft: false
tags: ['Web Development']
---

How do you make certain JavaScript sequentially preloads your images? For instance if you have 10 images and the size of image 9 is the smallest then it's most likely JavaScript (internet explorer rather) will load image 9 first. Then the second smallest or perhaps in no true order. However there are times when you need to make certain the images are loaded in sequence.... The following accomplishes this -- figured it out after a lot of problems:

```javascript
 function Preload() {
    var t = new Image;
    t.src = AlbumImages[loaded];
    t.onload = function () {
        loaded++;
        Preload();
    }
} 
```

Where AlbumImages is an array of strings that contains URLs to images and loaded is a global variable starting at 0.