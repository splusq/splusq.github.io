---
title: 'How to check if Integrated Windows Authentication is available?'
date: Mon, 07 Mar 2011 13:13:32 +0000
draft: false
tags: ['Web Development', 'Security']
---

If you've ever worked on an intranet site, you often want to know, programmatically, if a client that's accessing your website has the ability to automatically login to your site (Integrated Windows Authentication). With Integration Windows Authentication, Windows can basically send (as a HTTP header) the currently "logged on" username. So your application can access this LOGON\_USER HTTP header and go from there. I won't get into the details of how to setup integrated windows authentication, but basically the idea is you go into IIS and for a given resource (a folder or a file) on your site you change the directory security to Integrated Windows Authentication. Once this is done, when a client visits your local site (the definition of local depends on the gateway or group policy), IE switches to the "intranet site" profile which allows the automatic authentication. That's the broad picture. But unfortunately, if the same users visits that same website from another location, say from their home, they will end up getting the ugly NTLM authentication box - and most of the times they don't know what to do. Partly because their experience has changed, at work they were simply "logged in" without doing anything and now they have to login and often times need to prefix their username with domain username format. That's not good. So it's useful to be able to find out programmatically if integrated windows authentication is available, if it is then you simply login using the integration authentication, otherwise you provide the user with a clean and simple forms authentication system. So let's start with some JavaScript code:

```javascript
var autoLogin = true;

try {
	var dom = new ActiveXObject("Msxml2.DOMDocument");
	dom.async = false;
	dom.load("ntlm/spacer.gif");
}
catch (e) {
	autoLogin = false;
} 
```

The code makes an AJAX call to a resource that you know is locked with integrated windows security. If the AJAX calls succeeds you know that your client has integrated authentication get to the resource. If it does not you know something's wrong, either they are using FireFox, or perhaps they are at home, whatever the case, your client cannot get to the resource without authentication. This is great, but we don't want the user to see an error message when the AJAX call fails so we wrap it around a try/catch block. The catch block essentially sets the autoLogin=false because something did not work out. That's it - that's a nifty little trick to check if your users can use integrated windows authentication.