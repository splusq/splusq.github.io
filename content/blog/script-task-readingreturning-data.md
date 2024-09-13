---
title: 'Script Task reading/returning data'
date: Fri, 25 May 2007 23:58:45 +0000
draft: false
tags: ['Uncategorized']
---

When you are using the Script Task in SSIS it's often useful to have the script task return data, and also at times useful to have access to external data. Once you drop a Script Task onto the designer and go to it's editor, under the script tab you'll find two attributes: ReadOnlyVariables, ReadWriteVariables. The first allows the script to access external **user variables** for reading, while the latter allows write access. It's important to realize that we are talking about user variables which is usually under the _User::_ namespace. So every variable you list must be prefixed by: User::. If you don't the script assumes System::. So here's a screen shot of what a script task might look like: [![Script Task (Variables)](/2007/05/script-task1.thumbnail.jpg)](/2007/05/script-task1.jpg "Script Task (Variables)") Once you click on the design script you get an editor where you are allowed to type VB.NET code. At this point SSIS script tasks do not support C#. Here's a sample of how you can access a user variable and set a value to it. As you can see from the sample code below, SSIS variables are not typed:

```basic
 Public Class ScriptMain
	Public Sub Main()	
		Dim Var As Variable = Dts.Variables("User::DataFiles")
		Var.Value = Table
		Dts.TaskResult = Dts.Results.Success
		...
	end Sub
end Class 
```

Variables are read the same way:

```basic
 Dts.Variables("User::LoadedFiles").Value 
```

If you find yourself using the script task often then you might consider other ways of transforming and manipulating your data, as they can get extremely difficult to debug.