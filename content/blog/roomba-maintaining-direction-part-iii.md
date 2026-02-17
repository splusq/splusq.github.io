---
title: 'Roomba: Maintaining Direction Part III'
date: Sat, 11 Dec 2004 00:56:27 +0000
draft: false
tags: ['Robotics']
---

So far I have discussed the following topics:

1.  [Introduction to our intelligent Roomba Vacuum cleaner.](/blog/vacuum-cleaner/)
2.  [How a torus (smart array) is used to maintain the state of the cleaning surface.](/blog/torus-smart-array-part-ii/)

Today I will discuss how we managed to maintain the direction we are traveling in.

What we did was to connect two light sensors on both sides of the robot. Then we attached a pinwheel (printed on a paper; see diagram below) attached to the motor using gears, right in front of the sensor. As the motor moved the pinwheel moved and at the same time we could use the sensors to see how many blacks we have seen on the LEFT motor and how many blacks we have seen on the RIGHT motor. A typical straight line direction will see same number of both black and white stripes (because you expect both the left and the right motor to move the same amount of distance causing the same number of black/white alternations); whereas a typical turn (90 degrees) will see more black on one motor compared to the other (once again because during a turn one of the motors is turned off and the other is run at full speed in order to make the turn happen).

![Pinwheel used to maintain direction](/2004/10/pinwheel.jpg)

Internally the system was designed to run using two threads. One that constantly monitors what the reading from the LEFT sensor and compared this value to the reading from the RIGHT sensor. If the system is meant to go straight and we are getting different values from the LEFT or the RIGHT sensors then they would be adjusted (by temporarily shuting down the motor with higher values) until both again have the same value (+/- some intrinsic error).

This way we were able to maintain direction either going straight or even making 90 degree turns. Next time I will present actual C code and discuss the details of the code and why we choose a simple two threaded model.