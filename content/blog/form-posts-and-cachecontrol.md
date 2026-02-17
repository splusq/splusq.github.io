---
title: 'Form posts and CacheControl'
date: Fri, 25 Feb 2005 23:56:02 +0000
draft: false
tags: ['Web Development']
---

Let's say you have an HTML form which posts to another page, and that page in turn posts to other page(s) (maybe even to itself). Consider what happens in a very common scenario such as this:

1.  User visits page 1
2.  Types in username and password
3.  Posts to page 2 (valid user)
4.  User sorts a column in page 2 which posts the column name and the order to page 2.
5.  Page 2 reloads.

Now if the user pushes the back button when he/she is step 5 he/she will see one of two things: (1) Either the previous page will load (which is the behavior we would like to have) OR (2) the infamous "Warning: Page has Expired" message will appear in the screen.

Most developers seem to assume that the latter is how forms work and ignore this irritation, specially since there is an alternative, hit refresh and press retry -- everything works (well not quite! strange things happen when you hit refresh, more on that some other time)!

The proper way to get the desired behavior is to use CacheControl. In an ASP page you can control how the page is cached. You get the warning message when there is no caching. That means you are saying: "always get a new file from the server. Do not cache anything". When ASP returns to the server it realizes that it was a post and does not automatically re-post the data for you (until you hit retry). You can mark a page as no-cahe using the following statement:

```csharp
Response.CacheControl = "no-cache"
```

However, if caching is allowed then a cached-version of the page is displayed when the user hits the back button, you can mark a page for caching using the following statement:

```csharp
Response.CacheControl = "public"
```

You can turn your caching method on/off at different times of your pages. However make sure the line appears before any output goes to the browser. For instance, you might switch to caching for almost-static pages but want to turn off caching when dealing with forms and posts.