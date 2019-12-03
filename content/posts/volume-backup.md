---
title: 'Volume Backup'
date: Fri, 09 Apr 2004 16:23:20 +0000
draft: false
tags: ['Uncategorized']
---

I am trying to work on a system/method to perform incremental backup over the network (using SAMBA). Here are some of the steps I have taken...

First I decided on the set of data that needs to be backed up. Since the very beginning all my data files (such as documents, programs, musics and so on) are kept in a completely separate drive. This drive (although large in size) is used only for my personal stuff and I never install anything or create any dependencies on this drive. This helps me be independent in case of an operating system crash.

Secondly my web, mail-server and certain other configuration files are kept on a completely different computer (onupol). One of the things that needed to be done was move some of the MySQL databases to a regular file. In case of a system crash I could easily restore the database. I have done this using the following code:

```bash
mysqldump -A -u root -p > DbDump
```

This dumps all the MySQL databases into the file DbDump. The restore can then be done just by executing DbDump.

To automate this process I have written a bash-script that contains the following:

```bash
 #!/bin/bash
mysqldump -A -u root --password="yourpasswordhere" > DbDump 
```

I saved this file as: savedb. I then needed to give execute permission to the file -- this is done using:

```bash
 chmod u+x savedb 
```

One final thing. I decided that this needs to be done every-week. So I wrote this cron job (as root) to do just that. First here's how you start the cron-file:

```bash
 crontab -e 
```

Then, write this job

```bash
 0 1 * * 7     /root/savedb 
```

Save the file. This job executes every sunday (7) at 1:00AM.

This takes care of most of the stuff. Then I used Microsoft Windows 2000 Server Backup to backup my windows/linux stuff into one single backup file.

There are certain tricks to that first I had to mount (or share) the Linux computer over SMB (using samba).

**Note** if you don't run your Windows 2000 server/Linux server as a PDC it is absolutely essential that your windows username and password match your samba username and password otherwise you will have to manually login everytime!

Then go to My Computer Right click any drive tools -> Backup. This wizard will guide you through the process of backing up the necessary folders and drives. My first option was to do a normal backup, then from that point on I will be doing incremental backup every month.

If you need extra details on any of the above steps let me know!