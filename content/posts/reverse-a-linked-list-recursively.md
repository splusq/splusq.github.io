---
title: 'Reverse a linked list recursively'
date: Wed, 23 May 2007 23:26:12 +0000
draft: false
tags: ['Uncategorized']
---

A question came up the other day while I was helping a friend of mine about recursively reversing a [singly linked list](http://en.wikipedia.org/wiki/Linked_list). The moment I heard about it I came up with a solution, but turns out the solution requires the function to accept the following signature:

```c
List* reverseList(List *l);
```

This actually poses a problem because there's no way to keep track of the head, or maintain any back pointers which could've been done if the function accepted a second parameter. Anyway, so I thought about it and initially started with some of the base cases:

```c
List* reverseList(List *l) {
	if(l==NULL) return NULL;
	if(l->next==NULL) return l;
	??? 
```

Now comes the tricky part. What do you do in the general case? If you think of two nodes, you realize that you have to change the next->next pointer to the current node, change the next pointer of the current node to the previous node (null if it's the last node) and you are done! The idea is the same with nodes more than two, the only thing you have to be careful about is when you make the recursive call. One way to grasp the idea is to realize that you have to force the stack to get as deep as possible (get to the next to last node) and set it's next node to null and then work yourself backwards. With that thought, I came up with this:

```c
 List* reverseList(List *l) {
	if(l==NULL) return NULL;
	if(l->next==NULL) return l;
	reverseList(l->next); 
```

Finally, I needed to reorder the next links as the stack returns back:

```c
 List* reverseList(List *l) {
	if(l==NULL) return NULL;
	if(l->next==NULL) return l;
	reverseList(l->next);

	l->next->next = l;
	l->next = null;
	return l;
} 
```

There it was that was the solution. Notice I ignore the return statement in the recursive call. This perhaps isn't very intuitive but since I am looking ahead one I don't necessarily need a pointer to the last element, only until n-1.