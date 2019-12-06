---
title: 'Old bugs are harder to catch'
date: Wed, 20 Apr 2005 03:18:49 +0000
draft: false
tags: ['Uncategorized']
---

About three years ago I worked on an application that in-part had to blend in with several other applications. Among many features one of them was to automatically generate something known as deadlines. The actual details are far to complicated to explain in details here, so I will use a simpler example.

Assume that you have four types of documents each have their own specific way of computing deadline. One of them follows a rule like:

1.  The deadline for document A is the fourth work day of the month
unless the fifth of the month is a Friday and that friday is a workday.2.  If however the month for which we are computing the deadline for is August then the deadline is 3rd Friday unless it's happens to be a holiday; in that case then it's the first available workday since the 3rd Friday of the month.

As you can see the rule is not straight forward and involves several fine details that if programmed incorrectly will cause other systems to fail, since it feeds data to them. One such bug caused this app to fail yesterday and finally we found what the problem was. In order to see the bug I will first present to you a pseudo-code of the intial program:

```javascript
 if(!FifthIsFriday(CurrentMonth, CurrentYear)) {
	if(CurrentMonth.GetMonth() != "AUG") {
		Deadline = FindFourthWorkDay(CurrentMonth, CurrentYear);
	}
	else {
		Deadline = ThirdFriday(CurrentMonth, CurrentYear);
		if(IsHoliday(Deadline)) {
			Deadline = FindNextWorkDay(Deadline);
		}
	}
}
else { // Fifth is a friday
	if(IsHoliday(DateValue(CurrentMonth+'/'+5+'/'+CurrentYear)) {
		Deadline = FindNextWorkDay(DateValue(CurrentMonth+'/5/'+CurrentYear));
	}
	else {
		Deadline = DateValue(CurrentMonth+'/'+5+'/'+CurrentYear);
	}
}	

/*
Output deadline
*/ 
```

Now the corrected code.

```javascript
 if(CurrentMonth.GetMonth() != "AUG") {
	Deadline = FindFourthWork(CurrentMonth, CurrentYear);
}
else {
	if(!FifthIsFriday(CurrentMonth, CurrentYear)) {
		Deadline = ThirdFriday(CurrentMonth, CurrentYear);
		if(IsHoliday(Deadline)) {
			Deadline = FindNextWorkDay(Deadline);
		}
	}
	else { // Fifth is a friday
		if(IsHoliday(DateValue(CurrentMonth+'/'+5+'/'+CurrentYear)) {
			Deadline = FindNextWorkDay(DateValue(CurrentMonth+'/5/'+CurrentYear));
		}
		else {
			Deadline = DateValue(CurrentMonth+'/'+5+'/'+CurrentYear);
		}
	}	
}
/*
Output deadline
*/ 
```

As you can see both are very alike. The only difference between the two was the first checked weather Fifth was a work day and if it was it would try to schedule that as the deadline regardless of the month. However, the second code checked weather it was August first before doing FifthIsFriday.

The worst part of testing / writing a calendar related program specially one that deals non-deterministic time (this program could output the deadlines for 2030) is very difficult to write because date intrinsicly propose so many test cases. This bug was not caught for the past three years because the past three years none of the Augusts had fifth as friday. However this year, the 5th of August happened to be a friday and the program was computing incorrectly.

I then thought to myself how would I have caught this bug even before causing such a problem? And the only conclusion I could come to was EXTREMELY detailed requirements; but we _already knew_ that!