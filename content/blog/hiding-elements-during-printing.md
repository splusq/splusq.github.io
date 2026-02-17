---
title: 'Hiding Elements During Printing'
date: Tue, 20 Apr 2004 02:15:13 +0000
draft: false
tags: ['Web Development']
---

I work in an environment where my collegues surrounding me strongly believe there is no difference between "Microsoft Excel" and the web. One of the arguments I almost always hear: "...why can't you do it? it can be done in excel -- see!".

So that's how it all started; Excel apparently has a feature that allows a user to hide certain elements from the spreadsheet during print-out; I am not sure how exactly it's done, I am not an Excel expert.

Obviously I had to come up with a way to handle this issue for the web. As it turns, it isn't difficult to do. What you do is the following:

1.  I strongly suggest you work on the print features (such as this) at the end of the life-cycle of the application. This will save you lot of double working (such as oh I changed this, so I have to change it there...)
2.  In the rest of the document when I refer to style-sheet I am referring to either your global style sheet or the style sheet you are using in the page that you want to include this feature.
3.  Add an element in the style sheet like this:

```css
.hide-for-print {
    display:block;
}
.hide-text-for-print {
    display:inline;
} 
```
4.  Now enclose the complete style-sheet between the following class:
```css
@media screen {
    /* include everything here */
} 
```
5.  Now make a copy of the style sheet, call it: `print-<something>.css` _(change <something> to something)_
6.  Open the `print-<something>.css` file
7.  Change the `@media screen` line to: `@media print`
8.  Also find the `.hide-for-print` and `.hide-text-for-print` elements in this `print-<something>.css` file
9.  Change it to:
```css
.hide-for-print {
    display:none;
}
.hide-text-for-print {
    display:none;
} 
```
10.  Save all the files
11.  I am assuming you have something like this in your HTML file:
```html
<link rel="stylesheet" href="/styles/something.css" />
```
    
    Change it to the following
    
```html
<link rel="stylesheet" media="screen" href="/styles/something.css" />
<link rel="stylesheet" media="print" href="/styles/print-something.css" /> 
```

12.  Now surround all the elements you want to hide with:

```html
<div class="hide-for-print">
<!-- elements you want to hide here ... -->
</div> 
```

If you want texts to be hidden during print you should do:

```html
<span class="hide-text-for-print">.... text to be hidden during print ...</span> 
```