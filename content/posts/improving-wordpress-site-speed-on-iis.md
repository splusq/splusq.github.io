---
title: 'Improving Wordpress site speed on IIS'
date: Sun, 24 Mar 2013 19:03:14 +0000
draft: false
tags: ['Uncategorized']
---

One of the first things I did when I started this blog was to change the way static files are cached by IIS to improve site speed. Static files are things that aren't going to change, things like CSS, JavaScript, images, documents, and so on. With IIS this is actually quite easy to achieve, you can start by making the following change to your web.config:

```xml
<system.webServer>
  <staticContent>
    <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="7.00:00:00" />
  </staticContent>
</system.webServer> 
```

There's a set of file extensions that's already mapped to staticContent - so IIS only applies this change to all static contents. Also notice that we are caching the content on the client side. This means returning visitors will see a performance boost and we cache it for 7 days on the client side. If you have file extensions that are not part of the standard staticContent extension list, you can easily add it by doing:

```xml
<staticContent>
  <mimeMap fileExtension=".woff" mimeType="font/x-woff" />
</staticContent> 
```

But what about improving performance for first time visitors? There's a key change you can do to see a significant difference:

```xml
<urlCompression 
    doDynamicCompression="true" 
    doStaticCompression="true" 
    dynamicCompressionBeforeCache="true"/> 
```

I strongly recommend reading the [IIS documentation for urlCompression](http://www.iis.net/configreference/system.webserver/urlcompression). Next time I'll talk about how I used [wp-supercache to dramatically improve performance](http://wordpress.org/extend/plugins/wp-super-cache/) for both first time and returning visitors.