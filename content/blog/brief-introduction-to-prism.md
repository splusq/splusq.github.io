---
title: 'Brief introduction to PRISM'
date: Tue, 08 Mar 2011 14:55:10 +0000
draft: false
tags: ['.NET', 'Architecture']
---

Before I go into the technical details of this article. I want to provide some context for this discussion. So we are using [PRISM](http://compositewpf.codeplex.com/) to develop a user-interface (UI) platform in the context of a Silverlight (but they apply to WPF also). The idea behind the UI platform is we will provide the basic functional look and feel and various plumbing (such as security, service encapsulation, messaging and exception handling, among others) and allow others to develop modules/components on top. PRISM allows us to do this quite well. Let’s assume for a second, you have a system where a user can initiate an order, and then track the shipment status of that order. PRISM allows us to separate the concerns of this applications into two distinct tasks. (1) ordering, and (2) shipment tracking (and perhaps even further, but let’s keep it simple). Each of these could be developed as separate modules, and therefore separate DLLs, and possibly by separate teams. In the end, with PRISM, we can **_dynamically compose_** these at runtime even though they are independent components. So if you can imagine for a second, all the base functionality that we talked earlier is hosted in a component itself usually referred to as the Shell. The Shell is like the hosting application, where everything comes together. If there’s any resemblance to web applications, as Masterpage is to a ASP.NET page, a Shell is to a Silverlight/WPF application. That’s one way to look at it. So that’s the general overview for our discussion. And you can get a lot of good content about PRISM from Karl Shifflett, John Papa. With PRISM (and also few other technologies like MEF) what we are able to do is we are able to surface modules on the fly based on a modules catalog (one of many ways). One key architectural decision we made early on was that the Shell would be independent of the modules – meaning the Shell does not have any strong references to the modules. This allows us to introduce new modules without actually recompiling the Shell. But many examples out there do exactly that, they either (1) add strong references to Modules from the Shell or (2) they use something known as a ModulesCatalog file. Which is a XAML file that weakly defines the modules list. But again, the ModulesCatalog.xaml file is backed into the Shell – and therefore part of the XAP. That brings us back to the some of the same restrictions. Part of the problem we have is we have several teams, teams that are out of control, developing modules. 

All these modules contribute to the same application. Obviously a single team owns the Shell and they are responsible for it, and have access to it. So if there’s any dependency whatsoever on the shell (such as adding references, or editing a ModulesCatalog file) during the onboarding process of a module, then we have a problem. **That’s where dynamic modules catalogs come in.** With this approach we are able to decouple the shell even further, and move the catalog to a separate project. In our case, we moved the ModulesCatalog to the hosting web project. So let’s assume you have a `ModulesCatalog` file that looks like this:

```xml
<Modularity:ModuleCatalog xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
xmlns:sys="clr-namespace:System;assembly=mscorlib"
xmlns:Modularity="clr-namespace:Microsoft.Practices.Prism.Modularity;assembly=Microsoft.Practices.Prism">
<Modularity:ModuleInfo Ref="Modules.Order.xap" ModuleName="OrderModule" ModuleType="Modules.Order.OrderModule, Modules.Order" InitializationMode="OnDemand" />
</Modularity:ModuleCatalog> 
```

And we want to load this dynamically from the Shell (which is a Silverlight application).  We can do this during the Application\_Startup which generally initializes the bootstrapper. However, in our case we load the ModulesCatalog via a WebClient call first, and send the stream to the boostrapper.

```csharp
private void Application_Startup(object sender, StartupEventArgs e) {
        var client = new WebClient();
        client.OpenReadCompleted += new OpenReadCompletedEventHandler((s, ev) => {
            var bootsrap = new Bootstrapper(ev.Result);
            bootsrap.Run();
        });
        
        client.OpenReadAsync(new System.Uri(@"Metadata\ModulesCatalog.xml", System.UriKind.Relative));
    } 
```

Once that’s done, we can :

```csharp
protected override IModuleCatalog CreateModuleCatalog() {
    return Microsoft.Practices.Prism.Modularity.ModuleCatalog.CreateFromXaml(stream);
} 
```

Going forward I will talk more about PRISM, and MEF (Modularity Extensibility Framework). If there’s anything specific around these technologies you would like me to talk about please bring them up in your comments.