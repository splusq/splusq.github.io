---
title: 'JavaScript Performance - Part I'
date: Sat, 30 Oct 2004 02:34:33 +0000
draft: false
tags: ['Uncategorized']
---

This is the first in a series of articles I plan to write on improving performance in JavaScript heavy web-applications. More specifically I will discuss some of the ways you can improve on JavaScript based animations, reducing object chains, cross-browser DOM, clever ways of writing/designing objects and so on. 

**Improving on Animations** 

No matter what type of animation it is if it's written using JavaScript it's most likely going to make use of setTimeout or setInterval. Both of these function work like a timeline in Flash and therefore it's ideal for doing animations. To begin with setInterval is much faster than setTimeout. The reason is setInterval causes a function to continously run until it's stopped using clearInterval, where as setTimeout runs a function after a given time and stops; therefore if you wanted to animate something using setTimeout you would somehow have to keep calling setTimeout (until the animation ended) where as in setInterval this is done only once. The down-side of this is setInterval is supported only by Internet Explorer so to get **really** high performance you might want to write one version (separate files) of your JavaScript that's geard towards Internet Explorer and another for Netscape (which supports setTimeout). _Next time I will discuss how to run multiple animations at once using one-thread (or one setTimeout or setInterval)._