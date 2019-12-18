---
title: 'Using the MVVM pattern on web applications – Part I'
date: Thu, 28 Feb 2013 09:01:15 +0000
draft: false
tags: ['Uncategorized']
---

In this series I will be discussing how the MVVM pattern can be used for almost any type of web application. MVVM (a variant on the MVP pattern) - is a really powerful pattern that can be used in UI applications. Traditionally these UI applications has been WPF and Silverlight. In this article I will show you how you can use the same principles to build web applications. With MVVM you have the following three ideas:

*   **M**odel - Represents the shape of the data. This can be your entity framework model or any other object - that describes your entities in view.
*   **V**iew - This is where you represent your UI elements. Textboxes, dropdowns, anything you see on the screen. This mostly has been XAML.
*   **V**iew**M**odel - This is where you represent the UI specific logic.

Now why would you ever want to do something like this? The reason is when you write a web application or other UI application today you tend to write a lot of the UI-related logic in the code behind of the UI itself. This couples that logic to that specific view or page. You can't reuse it. You also can't easily test that logic because your UI (or the view) and logic about it are coupled together. If you wanted to test it you would have to use tools like [Selenium](http://docs.seleniumhq.org/) and automate your UI clicks. Not fun. With MVVM because these are all separate, you can reuse the logic from a ViewModel across multiple views. Your ViewModels are reusable, which means you can create a view for say a mobile app, another for a large screen monitor, two views, one ViewModel. And lastly, because the ViewModel is a separate object which has nothing to do with the view - you can easily test it like you would any normal class. But how can the ViewModel which is completely disconnected from the view talk to the view? It's almost like we need a mechanism to de-couple ourselves from the UI but have a mechanism to update the UI. **Enter Binding.** If you ever used WPF or Silverlight you know one of the powerful concepts in the framework is support for binding (this is different from asp.net data binding). The way data binding works is that every view both in WPF and Silverlight (any XAML view) has this notion of a DataContext. Data context is an object that you can assign anything into and it is responsible for providing the data necessary for the view to function. So for example, a DataContext could be something as simple as:

```csharp
 // a simple example
this.DataContext = new { FirstName = "Salman" };

// or you can load data from a DB
this.DataContext = repository.GetCustomerById(1); 
```

Once you have the data, within the view you can bind properties from the data context object:

```html
 <TextBox Text="{Binding FirstName}" /> 
```

What this is saying is bind the FirstName property of the underlying model against this view. So if your underlying model is set to the value "Salman" the textbox text will display the text Salman. Think for a second what you would have to do say in a traditional ASP.NET application. You have to create a Textbox, give it a name, and in the code behind, somewhere in page load, do something like TextBox.Text = FirstName - something like that. And this is just in one place, when the data changes due to a database load you have to update the textbox again. That's a lot of grunt work - and error prone! So what about displaying changes that happen in the underlying model? Say your FirstName in your underlying model changes from Salman - to Sal - how can we reflect that back to the UI? This happens through a really simple interface called [`INotifyPropertyChanged`](http://msdn.microsoft.com/en-us/library/system.componentmodel.inotifypropertychanged.aspx). INotifyPropertyChanged has an event named PropertyChanged. The idea is anytime the underlying model changes you raise this event with the name of the property that changed. The underlying framework does the rest. And lastly, what about when a user types something into the UI - how does the underlying model update? For that all we need to do is update the Mode to TwoWay in the binding. That's it. Okay let's quickly summarize what we have:

*   Data context - any object that provides the necessary data to the view
*   Data binding - the idea of binding properties from your data context object to the view
*   Bi-directionality - the ability for the UI to update the underlying model and vice-versa

So the key takeaway here is that MVVM is great. It helps me write disconnected logic for my view. But how can we use a pattern like this in a web application? Because a key ingredient for MVVM to work is Binding - which does not exist in web - and rightfully so. WPF and Silverlight is able to support binding because both the View and the ViewModel are in-process and the framework can listen for events. But in a web application the view is a HTML page, which lives in the client side in a browser somewhere, and the so-called ViewModel is the code behind which lives in the server side somewhere. So this is where magic comes in. With the help of bunch of different toolsets, like [Knockout](http://knockoutjs.com/), [MVC](http://www.asp.net/web-api), and [WebSockets](http://msdn.microsoft.com/en-us/library/system.net.websockets.websocket.aspx), we will achieve similar results - and it's just awesome. Because you will feel like you are writing .NET code without any UI and suddenly you will have a UI application. Sounds like magic, doesn't it? But for that you will need to wait till Part II of this discussion.

*   Using the MVVM pattern on web applications – Part I
*   [Using the MVVM pattern on web applications – Part II](/blog/using-the-mvvm-pattern-on-web-applications-part-ii/)
*   [Using the MVVM pattern on web applications – Part III](/blog/using-the-mvvm-pattern-on-web-applications-part-iii/)