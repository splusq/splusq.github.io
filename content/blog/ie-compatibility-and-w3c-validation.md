---
title: 'IE Compatibility and W3C Validation'
date: Mon, 04 Mar 2013 08:17:23 +0000
draft: false
tags: ['Web Development']
---

I've always been a junkie when it comes to [markup validation](http://validator.w3.org/check?uri=http%3A%2F%2Fsalmanq.com%2F&charset=%28detect+automatically%29&doctype=Inline&group=0&user-agent=W3C_Validator%2F1.3). It's important to me that my sites are 100% XHTML/strict compatible. But recently I came across a situation that I thought I simply couldn't get around. In this article I am going to show you how you can add the X-UA-Compatible attribute and at the same time validate your pages through W3C validation. My website had the following tag:

```html
 <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1"> 
```

This is a way to force IE to not display the compatibility mode button in the URL. Which I don't want anyway. While the X-UA-Compatible is a standard meta key, the value IE=edge is not, and therefore the validator gave me an error. So I finally figured it out, I removed the tag from my template and added the meta tag to the standard HTTP header via web.config:

```xml
 <system.webServer>
	<httpProtocol>
	  <customHeaders>
	    <add name="X-UA-Compatible" value="IE=edge,chrome=1" />
	  </customHeaders>
	</httpProtocol>
</system.webServer> 
```

Now both [W3C](http://validator.w3.org/check?uri=http%3A%2F%2Fsalmanq.com%2F&charset=%28detect+automatically%29&doctype=Inline&group=0&user-agent=W3C_Validator%2F1.3) and IE are happy.