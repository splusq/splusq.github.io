---
title: 'Using the MVVM pattern on web applications – Part III'
date: Wed, 06 Mar 2013 06:39:59 +0000
draft: false
tags: ['Architecture', 'Web Development']
---

We are at the finale of this three part series. [In part I](/blog/using-the-mvvm-pattern-on-web-applications-part-i/) we discussed the MVVM design pattern, [in part II](/blog/using-the-mvvm-pattern-on-web-applications-part-ii/) we looked at the overall architecture and how the server side fits into the picture. In this article we are going to look at the client side of this and close with some final thoughts. The best place to start with the client is the HTML - so here's the part of the HTML that's interesting:

```html
<tbody data-bind="foreach: Node">
    <tr>
        <td data-bind="text: Name"></td>
        <td style="text-align:right;font-weight:bold;" data-bind="text: Memory"></td>
    </tr>
</tbody> 
```

Notice that we are able to use a "data binding" like syntax similar to what we have seen in XAML using [Knockout](http://knockoutjs.com/). Basically we are binding a list of "node" elements as a row, and for-each row we want to bind the Name and Memory properties. What's really great about this is that the names of the properties came directly [from the server model](/blog/using-the-mvvm-pattern-on-web-applications-part-ii/). But how do we light this up? Meaning, how do we attach this page to the server side ViewModel? We do that using:

```javascript
$(document).ready(function () {
    new dataContext("Service.ViewModels.TaskViewModel");
}); 
```

Recall in part II, [we exposed a MVC Web API to create the ViewModel](/blog/using-the-mvvm-pattern-on-web-applications-part-ii/) - which accepted the name of the ViewModel we wanted to create. So in JavaScript we created a dataContext object that talks to this API endpoint and hooks the websocket communication. That's all. Let's take a look at that too now:

```javascript
 var dataContext = function (vm) {
    var self = this;
    var init = false;
       
    $.getJSON("/Service/Api/Attach/" + vm, function () {
        connectSocketServer(function (e) {
            self.update(e.data);
        })
    });

    this.update = function (d) {
        if (!init) {
            ko.mapping.fromJSON(d, {}, self);
            ko.applyBindings(self);
            init = true;
        }
        ko.mapping.fromJSON(d, {}, self);
    }
}; 
```

There you have it. Notice the getJSON call to have the server create the ViewModel for us, once that happens we use the websocket to communicate to the client and Knockout to apply the Binding. I hope you enjoyed this series and learned a few things. You can [download the full working source code](/2013/03/HtmlMagic.zip) - you're welcome to modify this and use it as you see fit. One thing I do want to mention though - this was written pre .NET 4.5, when Websocket was not part of the .NET BCL - so this source code uses [SuperWebsocket](http://superwebsocket.codeplex.com/). However, now that there is native support for Websocket the code should be refactored.

*   [Using the MVVM pattern on web applications – Part I](/blog/using-the-mvvm-pattern-on-web-applications-part-i/)
*   [Using the MVVM pattern on web applications – Part II](/blog/using-the-mvvm-pattern-on-web-applications-part-ii/)
*   Using the MVVM pattern on web applications – Part III