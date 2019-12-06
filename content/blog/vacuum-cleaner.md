---
title: 'Vacuum Cleaner'
date: Thu, 18 Nov 2004 00:13:09 +0000
draft: false
tags: ['Uncategorized']
---

For the past few weeks me and a team of three extremely bright computer science graduate students have been working on a vacuum cleaner. The idea is to design a robot that can clean a room _efficiently_; the key term here being efficiently. There is an existing product called: [Roomba](http://www.irobot.com/) which sells for about $300.00 dollars and does the same thing we are trying to do -- but it does it zero intelligence. Roomba works by vacuuming a room **long enough** to gurantee (probabilistically) that at least 90% of the reachable space within a room will be vacuumed. I say reachable because there could be areas in the room which is not reachable and that's not taken into account. Anyway, so in other words to vacuum a 100 sq. ft. room Roomba would spend around 10-15 (regardless of weather it's filled with objects or not -- because it has no way of knowing), where as a human being would easily vacuum the same room in under 2 minutes (83% gain) with perhaps zero unvacuumed areas. The reason Roomba has to vacuum so long is because it has no way of _tracking_ weather it has been to a specific area in the room (that's partially due to the fact that Roomba runs on only 1~2KB of memory). So a way of guranteeing better coverage is to wonder around in complete disarray for longer hoping to reach that "one" un-vacuumed area. Of course, no one really complains because who cares if it's vacuuming longer? It's cleaning better right?

**Our (Intelligent) Robot**

The robot we are working on vacuums with intelligence. For instance, it minimizes the time it takes to vacuum a room by reducing repetitions. Repitions can occur when some areas of the room is vacuumed more than once. First example what does the robot do when it finds something is blocking it to go further? Roomba simply goes back and turns right and tries to vacuum. This is a serious problem! Consider this scenario for instance:

![Possible path Roomba would take](/wp-content/uploads/2004/11/path-roomba.jpg)

The red cells are blocked, the current position of the robot is at point R and it's moving in the direction as shown above. In the diagram above the typical path Roomba would take is shown (with arrows and blue shade). Now consider this diagram:

![Possible path our robot would take](/wp-content/uploads/2004/11/path-ourrobot.jpg)

Once again the red cells are blocked, the current position of the robot is at point R and it's moving in the direction as shown above. In the diagram above the typical path our robot would take is shown (with arrows and yellow shade). You can clearly see Roomba's in-efficiency.

_More to come on this with example codes and perhaps the complete source-code._