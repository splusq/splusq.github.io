---
title: 'Automatic Printing'
date: Tue, 20 Sep 2005 23:42:20 +0000
draft: false
tags: ['Uncategorized']
---

**Summary:** Learn about automatic printing in a Windows platform using WScript.

Automatic printing is a subject you will find a lot of content about on the internet -- but most of it is useless. It's a massive collection of try-this-try-that. I was searching for some descent content on how to do scheduled automatic printing but spent far more time that I would like to. Finally I arrive at this code:

```basic
Option Explicit
Private Const OLECMDID_PRINT = 6 
Private Const OLECMDEXECOPT_DONTPROMPTUSER = 2 
Private Const READYSTATE_COMPLETE = 4 
Private Const PRINT_DONTBOTHERUSER = 1
Private Const PRINT_WAITFORCOMPLETION = 2

Dim pValIn, pValOut

With CreateObject("InternetExplorer.Application")
	'.visible=true
	.Navigate "http://yourpage/"
	Do Until .ReadyState = READYSTATE_COMPLETE AND NOT .Busy
	loop

	pvalIn = PRINT_WAITFORCOMPLETION XOR PRINT_DONTBOTHERUSER
	pvalOut = ""
	Call .ExecWB(OLECMDID_PRINT,OLECMDEXECOPT_DONTPROMPTUSER, pValIn, pValOut)
	.Quit
End With 
```

There something important to note here. The following lines are necessary for Windows 2003 Enterprise server:

```basic
pvalIn = PRINT_WAITFORCOMPLETION XOR PRINT_DONTBOTHERUSER
pvalOut = ""
Call .ExecWB(OLECMDID_PRINT,OLECMDEXECOPT_DONTPROMPTUSER, pValIn, pValOut) 
```

On a regular Windows XP machine you could get by simply doing:

```basic
Call .ExecWB(OLECMDID_PRINT,OLECMDEXECOPT_DONTPROMPTUSER)
```

This code works by opening a Internet Explorer window and loading a page and printing that page. Note that if that file loads a PDF file or an excel document then this won't work. This only works in plain HTML documents.