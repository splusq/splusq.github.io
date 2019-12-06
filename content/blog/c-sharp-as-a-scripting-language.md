---
title: 'C-Sharp as a scripting language'
date: Thu, 07 Mar 2013 04:49:07 +0000
draft: false
tags: ['Uncategorized']
---

[![scriptcs.net logo](/2013/03/scriptcs-logo.png)](http://scriptcs.net/) Here's a cool technology you might not have heard of: [scriptcs](http://scriptcs.net/). Gives you the ability to use C# as a scripting language. It does this using the [Rosyln compiler](http://msdn.microsoft.com/en-us/vstudio/roslyn.aspx). So here's an example below, taken directly from the scriptcs website:

```csharp
using System;
using System.IO;
using System.Web.Http;
using System.Web.Http.SelfHost;

var address = "http://localhost:8080";
var conf = new HttpSelfHostConfiguration(new Uri(address));
conf.Routes.MapHttpRoute(
	name: "DefaultApi", 
	routeTemplate: "api/{controller}/{id}", 
	defaults: new { id = RouteParameter.Optional }
);

var server = new HttpSelfHostServer(conf);
server.OpenAsync().Wait();
Console.WriteLine("Listening...");
Console.ReadKey(); 
```

This is a simple way to startup a self-hosted server. But look at the various missing pieces here, no project file, no solution, no classes, no references (they happen through nugget packages) just a quick, frictionless, server. [How elegant](http://codebetter.com/glennblock/2013/02/28/scriptcs-living-on-the-edge-in-c-without-a-project-on-the-wings-of-roslyn-and-nuget/). scriptcs was started by [Glenn Block](https://twitter.com/gblock) (he contributed to WCF, MEF, node.js for Azure, among other things).