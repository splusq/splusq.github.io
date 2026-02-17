---
title: 'Rendering Step 1 of … (ASP.NET)'
date: Thu, 27 Sep 2007 01:01:29 +0000
draft: false
tags: ['.NET', 'Web Development']
---

The MultiView control, Wizard control or even a custom panel-based control is very useful to collect large sets of data in wizard form; and it's often useful to give visual ques to the user as to which step he/she is in. Something similar to:

[![StepMaker-Screenshot](/2007/09/screenshot.jpg)](/2007/09/screenshot.jpg "StepMaker-Screenshot")

The following ASP.NET code/C# allows us to build a dynamic step control which grows or shrinks automatically given the number of views you have in a MultiView control, or the number of steps in a wizard control.

```html
 <asp:Repeater ID="StepMarker" runat="server">
            <headerTemplate>
                <div class="steps">
                    <h2>Step</h2>
                    <ol>
            </headerTemplate>
            <itemTemplate>
                <li<%#ReportingPanel.ActiveViewIndex==Int16.Parse(DataBinder.Eval(Container, "ItemIndex").ToString())?" class=\"active\"":""%>><%#Int16.Parse(DataBinder.Eval(Container, "ItemIndex").ToString())+1%></li>
            </itemTemplate>
            <footerTemplate>
                </ol>
                </div>
                <br style="clear:both" />
            </footerTemplate>
        </asp:Repeater> 
```

```csharp
 /* if we have arrived at the last step we no longer need to display the step markers */
if (ReportingPanel.ActiveViewIndex + 1 == ReportingPanel.Views.Count) {
	StepMarker.Visible = false;
}
else {
	StepMarker.Visible = true;
	int[] i = new int[ReportingPanel.Views.Count - 1];
	StepMarker.DataSource = i;
	StepMarker.DataBind();
} 
```

```css
.steps ol {
	margin:0px;
	padding:0px;
	font-family:'arial narrow',sans-serif;
}
.steps h2 {
	font-size:19pt;
	font-weight:normal;
	color:#0066cc;
	float:left;
	padding-right:20px;
}
.steps ol li {
	float:left;
	list-style-type:none;
	font-size:19pt;
	color:White;
	background:url(../images/step-shadow.gif);
	background-repeat:no-repeat;
	background-position:left top;
	padding:0px 0px 0px 10px;
	width:43px;
	height:40px;
}
.steps ol li.active {
	font-weight:bold;
	background-image:url(../images/step-glow.gif);
} 
```

**Images**

*   [StepMaker-Glow Background](/2007/09/step-glow.gif "StepMaker-Glow Background")
*   [StepMaker-Shadow Background](/2007/09/step-shadow.gif "StepMaker-Shadow Background")