---
title: 'Speaking about events technically'
date: Fri, 18 Jun 2004 05:31:16 +0000
draft: false
tags: ['Uncategorized']
---

The other day I was working on something in JavaScript and I thought I would share that with you. Before I can begin though I have to describe the scenario.

I have an image in an HTML page (lets call it: MyImage). I dynamically attach an event to the Image's onload method. Due to other events on the page MyImage's SRC attribute gets changed to something else. Now logically the onload event should execute again once the new SRC element gets loaded. But guess what? It doesn't. onload is known as a: "recursive descent" event meaning it descends recursively from the BODY; so unless the BODY's onload event doesn't change (which doesn't happen until reload/refresh) MyImage's onload event does not fire.

So how can you check if the new SRC element got loaded? The trick is to use the readyState event (only in IE) you can do MyImage.readyState to check to see the status of the image, the finite-automaton for all readyState objects is the following:

```bash
uninitialized 	- Object is not initialized with data. 
loading 	    - Object is loading its data. 
loaded 		    - Object has finished loading its data. 
interactive 	- User can interact with the object even though it is not fully loaded. 
complete 	    - Object is completely initialized. 

uninitialized -> loading -> {loaded OR interactive} -> complete 
```