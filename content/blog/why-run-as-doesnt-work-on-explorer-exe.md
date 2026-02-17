---
title: 'Why run as doesn’t work on explorer.exe?'
date: Wed, 23 Feb 2005 20:54:39 +0000
draft: false
tags: ['General']
---

You will notice in windows no matter how you want to "Run as" explorer.exe it will not work. Almost all programs you can right-click or hold CTRL and right click to get the run as option but with explorer this doesn't seem work. You cannot even use the command line tool runas /u:administrator to run explorer.

Explorer.exe is a special task. It actually runs all the time and is responsible for the taskbar and other features on the Windows desktop, press CTRL+ALT+DEL right now and you will see explorer.exe is running, you can also kill the process (don't do it if you have important things going, but for the sake of experiment you can try) and you will notice your taskbar and your desktop will clear away. You can once again run the process by clicking File->New Task->explorer.exe

The reason why run as does not work with explorer is because when a new explorer instance wants to start it looks to the current desktop for an already running instance of explorer.exe to find out what needs to be done (for instance explore c:\\) once the new explorer knows what to do it takes over and the previous instance automatically unloads itself (they both share the same PID, we will talk about this more in detail some other time).

Since the current explorer is running as "you" the user on the system the run as option cannot force a new user on an already running instance. And that's why the run as feature does not work.

A workaround to this problem is to run iexplore to explore the drives (almost all things explorer can do iexplore can). You can do something like:

```batch
runas /u:administrator "c:\program files\internet explorer\iexplore.exe"
```

You can run the above command from the cmd prompt and it will prompt you for the administrator password, once in you can then explore the system (simply type: C:\\) with administrative rights! Aaron Margosis at Microsoft also [has some interesting thoughts on this](http://blogs.msdn.com/aaron_margosis/archive/2004/07/07/175488.aspx) if you want to read on.