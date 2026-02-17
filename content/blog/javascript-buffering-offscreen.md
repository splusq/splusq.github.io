---
title: 'JavaScript Buffering - Offscreen'
date: Tue, 06 Jul 2004 14:39:12 +0000
draft: false
tags: ['Web Development', 'Performance']
---

Just before I go to bed I thought I make an interesting post. The other day I worked on a site for a client; like most of my sites it was very heavy on style sheet (meaning most of the alignments and things were done with style sheet rather than tables, etc.) So what would happen is the page would load without style sheet for a sec. and then the style sheet would be applied. So for a split second the client would have to see the page without the style sheet -- which doesn't look professional.

So to resolve this issue, I tried using this JavaScript code (at the head section of everypage).

```javascript
 <script type="JavaScript">
<!--
if(window.offscreenBuffering) {
	window.offscreenBuffering=true;
}
//-->
</script> 
```

The first line checks to see if the browser supports offscreenBuffering (IE/Netscape/Opera does) if it does then it is applied to the page. What this does is the page is loaded completely in a buffer area (similar to double buffering in Java) and then displayed to the client. This makes it look as if the page loads faster too. That's because the buffer is a memory space that is not being rendered so the CPU can complete the page much faster.

By default offscreenBuffering is set to auto. Which leaves the browser to decide when to use offscreenBuffering and when not to. If your page doesn't have lot of data and but is heavy on styles then I would suggest using offscreenBuffering.

If your page contains a lot of data then the page will not be displayed until all the data is received and formatted, which might take a few seconds, in that case offscreenBuffering should explicitly be set to false and your server-side script shouldn't be buffered (assuming you are using one. In ASP set Response.Buffer=false, for PHP read about function ob\_start).

Hope that helps you building faster, faster pages!