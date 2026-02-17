---
title: 'Using Control Adapters'
date: Tue, 15 May 2007 04:55:43 +0000
draft: false
tags: ['.NET', 'Web Development']
---

ASP.NET 2.0 introduced a new way of modifying how ASP.NET renders a control. For example, when you say:

```html
<asp:textbox id="FirstName" runat="server" />
```

You have very limited control over how the control actually renders. You can specify a _cssclass_ to modify the look and feel, but the actual HTML rendering is limited to what's available from the ASP.NET server attributes. For example you cannot add client-side onfocus or onblur statements. This is where Control Adapters comes in. Control adapters allows you to modify the rendering of any server control, with complete freedom.

**Let's look at an example!**

First in order to use a control adapter you need to add a class to your App\_Code. This class will programmatically describe the various rendering behaviors you want to modify for a given server control. I usually put all my control adapters under one folder called: adapters. And each file under that folder is named after the server control I am trying to modify. In this case it's textbox.cs, because we are modifying the textbox control.

```csharp
 namespace UI.Adapters {
    public class TextboxAdapter : System.Web.UI.WebControls.Adapters.WebControlAdapter {
        protected override void  Render(HtmlTextWriter writer) {
            writer.AddAttribute("onfocus", "textFocus(this)");
            writer.AddAttribute("onblur", "textBlur(this)");
            base.Render(writer);
        }
    }
} 
```

The code above, adds two arguments onfocus=textFocus(this); and obur=textBlur(this) too all server side text boxes automatically. You also need to add a browser file under your App\_Browsers folder. If you don't have one right click your project add a Browser file. A browser is a XML file that describes how ASP.NET behaves depending on browser versions.

```xml
 <browsers>
  <browser refID="Default">
    <controlAdapters>
      <adapter controlType="System.Web.UI.WebControls.TextBox" adapterType="UI.Adapters.TextboxAdapter" />
    </controlAdapters>
  </browser>
</browsers> 
```

A refID of Default includes all modern browsers like Firefox and Internet Explorer > 5.5. Then the code specifies the type of control you want to modify in this case `System.Web.UI.WebControls.TextBox` and the Control adapter you want to use, again in this case it's: `UI.Adapters.TextboxAdapter`.