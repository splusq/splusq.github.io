---
title: 'Object Databases (ORDBMS)'
date: Wed, 10 Nov 2004 19:38:02 +0000
draft: false
tags: ['Uncategorized']
---

ORDBMS - Object Relational, Database Management system. Is an object oriented database management system. The concept at first is a bit suprising since relational database sort of by definition negates the concept of objects. In theory a relational database is a set of relation R; each table, formally known as relations, can be joined (cartesian product) to produce a new relation (which is an improper subset of R1xR2). There are many problems with this scenario. Lets say you want a database that contains sets of points where computers are physically located in a global positioning system. This hypothetical organization contains various departments, a set of departments makes up a local-organization, and a set of local-organization makes up an organization, to make situations worst, each continent can contain several organizations; and there are seven continents. This company is so large they have branches all over the seven continents. The point of this all is to find the minimal net-cost (where cost is distance) for communication between computers. Your product is expected to answers queries of this type:

```bash
what is the most optimal link between
continent 1 - organization 2 - local-organization 3 - department 0 and
continent 4 - organization 3 - local-organization 0 - department 0
... 
```

How would one go about storing this information in a relational database (while maintaining all the database-normalization factors?). NF-1 says your information has to be atomic; which means each field cannot contain more than one information. For instance, according to NF-1 it would be illegal to have something like this: (John, Doe). Because that field contains two information one: John (which is the first name) and Doe (which is the last name). Transitively it can implied that storing information like (x1,y1,z1) would be incorrect according to NF-1 since it contains more than one information.

Well we can take care of that. Maybe we will have three columns. One for the X values, another for the Y values and yet another Z values. That's sounds good. But now, how do we know which sets of points correspond to which computer?

As I noted earlier you need a database that can contain a set of points not just a single point. Once again this is what you need: N = { (x1,y1,z1), (x2,y2,z3), ... , (Xn,Yn,Zn) }. Notice that x1,y1,z1, x2,y2,z2 and Xn,Yn,Zn all correspond to the network N. If we store the data as suggested above then we loose the information which points correspond to which network. Because as far as the specification goes there can be infinetly many networks. Well you say you can take care of that as well. You add a fourth column that will tell you which network the points belong to.

```bash
N	X	Y
1	10	20
1	11.4	-100
2	2.2	90
2	..	..
2	..	.. 
```

So far so good. Now you can do:

```sql
select CONCAT(CONCAT(CONCAT(CONCAT("(",R.x1),","),R.y1),")")
from R where N = 1; 
```

... and you have all the points that belong to N(1). Since each department can contain several networks you decide to make another table D which will contain the following information:

```bash
 Department	N
  3215		1
  3025		2 
```

Good so far. Now you have taken care of yet another level. But there are so many levels, local-organization, organization, continent! Each of which will require you to add at least one more column and worst of all, you will have to manually bend the relational database model to work for you. The point of it all is it gets exponentially complicated to keep track of what's going on after a certain point -- and this is a good point to assume we are reaching the limitation of relational database.

**_Note:_** This is just for illustration purposes. Finding minimal-cost between nodes of computers is a typical graph problem -- and database is a completely inappropriate storage medium.

**Object-Oriented Database**

This is where object-oriented databases come in. Before I go into the details of why an object-oriented database is useful let me just differentiate between an object-oriented database and an object-relational database. Object-Oriented database is a database management system that can store objects. That means you can say a table R can contain an object of type C. A object-relational database on the other hand is still a typical relational database, but another layer is added beyond the relational optimizer layer which converts relational queries and outputs to object-based queries and object-based storage respectively. Notice the crucial difference between object-oriented database and object-relational database. Object oriented database are much much faster because the underlying DBMS understands and recognizes objects where as a object-relational database just simulates this process.

Last year I was working on a project called: Object-Relational DBMS. It's another layer written on-top of MySQL optimizer to view relations as objects. A solution for the above problem can be:

```javascript
 Object point
 x float,
 y float,
 z float;

Object computer
 pt point,
 network integer;

Object department
 network computer;

Object local-organization
 depts department;

Object organization
 lg local-organization;

Object continent
 org organization; 
```

Notice how in the object computer, you are able to use point as a datatype (which itself was an object). Although internally these are stored as relations, from the programmers' perspective it's not at all the case. Everything becomes much simpler to conceive.