---
title: 'JavaScript Performance - Part II'
date: Tue, 02 Nov 2004 02:58:36 +0000
draft: false
tags: ['Web Development', 'Performance']
---

_As I [discussed last week](/blog/javascript-performance-part-i) setInterval in general gives better performance compared to setTimeout._

Today I will discuss how in order to improve performance, one thread can be used to run multiple animations. Multiple animations come up in several scenarios. For instance, if you have a menu that does some animation onmouseover then although not clear at first this menu system could be running multiple animations at the same time. For example, let's say the animation takes 2 secs to run. Then in this 2 second time frame if the user highlights another menu item then the browser is actually running to threads one for each menu item. This is can bring down the performance of the system quite heavily.

Every animation can be broken down into discrete states -- where each state changes with time. For instance, a animation of a bicycle moving in a two-dimensional space can be broken down into it's x coordinates everytime the thread is run the x-coordinate of the bicycle is updated and the bicycle is moved. The same can be done with menu items.

Notice that onmouseover and onmouseout the background of the menu transitions in and transitions out respectively. Had I used one thread for each of those menu items and if the user quickly went over several menu items quickly then the performance would greatly sacrificed.

In the case of the menu above there is only one state to maintain: the background color information for each of the menu item. On mouseover the setTimeout is started (only **once** no matter how many mouseover occurs) and a primary function (sometimes also known as a runner) runs through all the menu items and decrements or increments the color of the background (decrements if mouseout, increments if mouseover). That's it!

Here's a sample code illustrating that idea:

```javascript
var MenuObjects = new Array();
var TimerObject = null;

function MenuEffectRun() {
	var remaining=0;
	
	for(i=0;i<menuObjects.length;++i) {
		if(typeof(MenuObjects[i])!="undefined" && MenuObjects[i][0]) remaining+=MenuGlow(i);
	}
	
	if(remaining && !TimerObject) {
		//change for non-IE browsers
		//TimerObject = setTimeout("MenuEffectRun()",10);
		TimerObject = window.setInterval("MenuEffectRun()",10);
	}
	else if(!remaining) {
		//change for non-IE browsers
		//clearTimeout(TimerObject);
		window.clearInterval(TimerObject);
		TimerObject = null;
	}
}
function MenuHighlight(Table) {
	var id = parseInt(Table.id.substring(1));
	Table.className='menuhover';
	
	if(typeof(MenuObjects[id])=="undefined") {
		MenuObjects[id] = new Array(Table,0,30,0);
	}
	else {
		MenuObjects[id][1] = 0;
		MenuObjects[id][2] = 30;
		MenuObjects[id][3] = 0;
	}
	if(!TimerObject) MenuEffectRun();		
}
function MenuNormal(Table) {
	var id = parseInt(Table.id.substring(1));
	//Table.className='menu';
	
	MenuObjects[id][1] = 0;
	MenuObjects[id][2] = 30;
	MenuObjects[id][3] = 1;
	
	if(!TimerObject) MenuEffectRun();
}

function MenuGlow(index) {
	var start = MenuObjects[index][1];
	var step = MenuObjects[index][2];
	var state = MenuObjects[index][3];
	var fstate = (state^1); // alternate transformation

	if(start<=step) {
		MenuObjects[index][0].style.background = "rgb(" +
			Math.floor(BackgroundTransform[state][0] * ((step-start)/step) + BackgroundTransform[fstate][0] * (start/step)) + "," +
			Math.floor(BackgroundTransform[state][1] * ((step-start)/step) + BackgroundTransform[fstate][1] * (start/step)) + "," +
			Math.floor(BackgroundTransform[state][2] * ((step-start)/step) + BackgroundTransform[fstate][2] * (start/step)) + ")";
			
		MenuObjects[index][0].style.color = "rgb(" +
			Math.floor(FontTransform[state][0] * ((step-start)/step) + FontTransform[fstate][0] * (start/step)) + "," +
			Math.floor(FontTransform[state][1] * ((step-start)/step) + FontTransform[fstate][1] * (start/step)) + "," +
			Math.floor(FontTransform[state][2] * ((step-start)/step) + FontTransform[fstate][2] * (start/step)) + ")";

    	MenuObjects[index][1]++;
		return 1;
	}
	return 0;
} 
```