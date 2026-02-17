---
title: 'Outlook’s vCalender using ASP'
date: Tue, 07 Dec 2004 03:47:28 +0000
draft: false
tags: ['Web Development']
---

As part of a continuing effort to build more functional and user-friendly products I am working on an ASP-based vCalendar concept.

Originally I was working on a training enrollment system where users could enroll/drop for a class, standby in a waiting list and things of that nature. My department has been using this product for sometime now and my boss is starting to realize that more people signup than actually showing up. One of the reason for this she argued was they are forgetting that they had a training. One way to remind them of this training is to exploit Outlook's reminder system.

In the current stage of the software once a user signs up for a class a plain-text confirmation e-mails gets sent to their mailbox. By adding an attachment of type: .vcs to every outgoing confirmation e-mail I hope to add an appointment to the users' calendar. Of course this assumes everyone is using Outlook; although this statement might not be completely true, the percentage here at UCLA is high enough so that we should put an effort to take advantage of it.

Below is the contents of a typical .vcs file:

```bash
BEGIN:VCALENDAR
PRODID:-//Microsoft Corporation//Outlook 9.0 MIMEDIR//EN
VERSION:1.0
BEGIN:VEVENT
DTSTART:20040426T070000Z
DTEND:20040426T073000Z
LOCATION;ENCODING=QUOTED-PRINTABLE:Test
TRANSP:1
SUMMARY;ENCODING=QUOTED-PRINTABLE:Test
PRIORITY:3
END:VEVENT
END:VCALENDAR 
```

Among other things it contains the title of the event (SUMMARY), it contains the location where the event occurs (LOCATION), the time when the event starts (DTSTART) and ends (DTEND) and finally the priority of the event (PRIORITY). In order for my web-application to send this .vcs attachment I have to dynamically generate this vcs file. Because each users could be signing up for a different class, at different and often times at different locations.

**Problems**  
The e-mail gets sent out using the default CDONTS object that comes with IIS. In order to send an attachment using CDONTS I have to use the .attachment property and point it to a file on the local server. The problem with this is that I cannot simply write to say: "appointment.vcs" this will cause problems when multiple people signup at the same time and the mail-server was in the process of sending the first mail. In that case two things could happen: (1) the mail-server (in my case Microsoft Exchange) usually locks the file which would cause any additional writes to this file to fail and therefore while the first mail is being delivered no other attachments can be prepared; or (2) the mail-server will not lock the file and allow the system to modify the contents, this could result in a terrible bug where the user would get an e-mail saying they signed up for a say Monday even though they actually wanted to signup for Tuesday. The reason I say this is a terrible bug is because it's not going to happen every time. It will only happen once in a while specially when the mail takes longer to deliver.

The solution is to write the file in different (unique) file names for each enrollment. This could be acheived by using the EnrollmentId from the database if there is one or some other uniquely identifiable information about the enrollment.