---
title: 'Solution to Line Tracker'
date: Sat, 02 Oct 2004 01:17:34 +0000
draft: false
tags: ['Robotics', 'Algorithms']
---

In my [previous post](/blog/line-tracker/) I discussed an algorithm that it is much better than the _general_ line following algorithm that one might think of at first. Last night I implemented the algorithm with NQC with descent results. Here's the solution:

```c
/***********************************************
	* Salman Quazi
	* CS-595EA
	* Date: September 19, 2004
***********************************************/
/* Constant Definitions */
#define SENSOR		SENSOR_1
#define LEFT_MOTOR	OUT_C
#define RIGHT_MOTOR	OUT_A

/* Speed settings */
#define NORMAL_SPEED 7
#define TURN_SPEED 2
#define DELAY	20

/* This setting depends on several conditions like: lighting, contrast between the tape and the floor ... */
#define LINE_COLOR 768
#define FLOOR_COLOR 710

int eye;

/* I have been thinking about taking the average color of the 
floor and the black tape for more reliable results */
int average_color = (LINE_COLOR+FLOOR_COLOR)/2;

task main() {
	SetPower(LEFT_MOTOR,  NORMAL_SPEED);
	SetPower(RIGHT_MOTOR, NORMAL_SPEED);
	OnFwd(LEFT_MOTOR+RIGHT_MOTOR);

	while(true) {
		eye = SENSOR;
		SelectDisplay(DISPLAY_SENSOR_1);

		if (eye <= FLOOR_COLOR) {
			SetPower(LEFT_MOTOR+RIGHT_MOTOR, TURN_SPEED);
			Fwd(RIGHT_MOTOR);
			Rev(LEFT_MOTOR);
			On(LEFT_MOTOR+RIGHT_MOTOR);
			while(SENSOR <= FLOOR_COLOR);
		}
		else if (eye >= LINE_COLOR) {
			SetPower(LEFT_MOTOR+RIGHT_MOTOR, TURN_SPEED);
			Fwd(LEFT_MOTOR);
			Rev(RIGHT_MOTOR);
			On(LEFT_MOTOR+RIGHT_MOTOR);

			while(SENSOR >= LINE_COLOR);
		}
		else
			continue;

		SetPower(LEFT_MOTOR+RIGHT_MOTOR, NORMAL_SPEED);
		Fwd(LEFT_MOTOR);
		Fwd(RIGHT_MOTOR);
		On(LEFT_MOTOR+RIGHT_MOTOR);
	}
} 
```

This was compiled with [NQC version 2.5 r3](http://bricxcc.sourceforge.net/nqc/release/index.html) on [RCX 2.0](http://mindstorms.lego.com/support/en/ris20.asp) (on a Windows 2000 Professional system).

Of course there is room for improvement. (1) Increase NORMAL\_SPEED and still be able to follow the line (by reducing latency speed). (2) If the system encounters an object while travelling the line then it should get around it somehow, and so on...