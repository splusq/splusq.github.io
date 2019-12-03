---
title: 'CSS: word-wrap'
date: Wed, 21 Apr 2004 23:58:46 +0000
draft: false
tags: ['Uncategorized']
---

You will notice this blog site will not overflow in the x-direction no matter what resolution you view this site in (I tried upto 640x480). When you have a div area and you are using PRE or XMP to view formatted contents (like I do in this page to view code) within that div -- this would be almost impossible to do. Here's the reason: unless otherwise stated a PRE or an XMP is displayed as is. Take a look at [this page](/sources/longpre.php) for instance. See how it overflows? If you had a shorter viewing area the overflow would be even greater. The solution is to explicitly say to allow word breaks. Take a look at [this page](/sources/longpre-fixed.php). It does not overflow anymore (assuming you are using IE). View the code to see the difference.