---
title: 'Re-Useable Images'
date: Thu, 27 Jan 2005 06:03:22 +0000
draft: false
tags: ['Uncategorized']
---

I was working on a project last week and part of the work was to create sort of like a template / menu / footer with an opening image. Initally I was positioning the title of the page as a regular h3 but then I thought of something great (I find it to be). [Take a look at this page](/sources/reuseable-images/) -- if you notice carefully you will see "Salman's Blog" is just a text. This was done using absolute positioning forcing the div and text to display over the image (with 40% opacity). Absolute positioning shouldn't be a concern in this case because it's meant to be used as headers which are constant and therefore have a definite pixel height from the top.