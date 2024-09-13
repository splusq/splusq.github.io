---
title: 'Switched to Hugo from Wordpress'
date: Wed, 11 Dec 2019 21:53:00 +0000
draft: false
tags: ['Uncategorized']
---

It's been a relief to move away from the bloat of WordPress. I decided to use [Hugo](https://gohugo.io) and host all of the content in [Github](https://github.com/splusq/splusq.github.io). I am even able to use [Github Actions](https://github.com/features/actions) to manage the continuous integration/delivery of the source to [Github Pages](https://pages.github.com) all entirely for free. While Github Pages, and Hugo both generate static html pages, I was  able to enable comments, via [utteranc.es](https://utteranc.es) which stores the comments in Github as issues. 

The overall experience is wonderful, clean and simple. The writing experience is also very enjoyable since markdown is a lot more predictable compared to WYSIWYG editors most WordPress authors rely on. 

The theme for the blog is [Hyde](https://github.com/spf13/hyde/tree/master), where I have made [minor tweaks](https://github.com/splusq/splusq.github.io/blob/release/layouts/_default/single.html) to the template to enable utteranc.es comments, via this simple JavaScript block:

```javascript
<script src="https://utteranc.es/client.js"
        repo="splusq/splusq.github.io"
        issue-term="title"
        theme="github-light"
        crossorigin="anonymous"
        async>
</script>
```