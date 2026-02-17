---
title: 'The trailing slash'
date: Thu, 28 Apr 2005 00:47:36 +0000
draft: false
tags: ['Web Development', 'Performance']
---

Sometimes a simple change as adding a slash may improve the performance of your web-applications. Consider this: the link [www.salmanq.com](http://www.salmanq.com) is slower than [www.salmanq.com/](http://www.salmanq.com/) (the only difference being the trailing slash). They will both work but the latter is faster. When this link: www.onupol.com is followed the web-server returns a 301 (Moved Permanently) pointing to www.salmanq.com/ that's one extra round trip to and from the server per link. If you have a site that's running without the trailing slash and you have 10K+ visitors you will certainly start to feel the consequence.