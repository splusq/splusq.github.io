---
title: 'Faster table rendering'
date: Fri, 11 Feb 2005 23:55:26 +0000
draft: false
tags: ['Web Development', 'Performance']
---

Unlike FireFox, Internet Explorer does not display a table until the complete table data has arrived. This can be looked at both positvely and negatively. The positives are, once the table is rendered it is not modified (width, height, positioning) because all the data has already arrived and IE can properly compute the width and height of the cells and rendered it top-down, right-to-left without making any other adjustments to the cells already displayed. This makes things look a bit better, but this approach is definetly quite slow.

In FireFox however, even after the table is rendered it is dynamically modified to "fit-the-contents" as data arrives to the browser. This technique of course is much faster because the data is shown as they are arriving.

I have been researching this for quite sometime now because I am working on a large dataset that is mainly used by Internet Explorer users and one of the requirements is to get a complete view of all the data. Since the data is displayed in a table it is not shown to the user until everything has arrived and aspects properly computed (by IE), which gives the user a feeling that the product is slow.

One way to FORCE internet explorer to show data as they arrive is to use the [table-layout:fixed](http://msdn.microsoft.com/library/default.asp?url=/workshop/author/dhtml/reference/properties/tablelayout.asp) style. This will cause internet explorer to stick to the width specified by the each td and display data as they arrive. If some data is longer than the specified width then it's wrapped, if however nowrap="nowrap" attribute is specified then data is clipped (cut-off).

However since that project I was working on is an ASP document I would somehow have to force the data to get sent to the browser every so often manully. This can be done quite easily. The first step is to set the Buffer to true. This can be done like this:

```basic
Response.Buffer = true
```

This should be done right at the beginning of the page (before any Response calls). The second step is to modify the looping structure a little bit to flush the data every now and then, this can be done like this:

```basic
Dim pos
pos = 0
while(not Records.EOF)
    pos = pos + 1
    ' print records here
    if(pos Mod 10 = 0) Then ' Every 10 records
        Response.Flush
        Response.Clear
    End If
    Records.MoveNext
wend 
```