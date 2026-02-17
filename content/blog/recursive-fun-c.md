---
title: 'Recursive Fun (C#)'
date: Thu, 14 Jul 2005 02:17:47 +0000
draft: false
tags: ['C#', 'Algorithms']
---

Recursive functions can be a lot of fun! I was playing around with a collegue of mine at work about solving recursive problems. And we both worked on a problem of deciding weather a number is the median in a list of numbers. Here's my solution:

```csharp
using System;

public class MedianRecursive {
	private delegate bool Function(int A, int B);

	private static bool GreaterThan(int A, int B) { return A>B; }
	private static bool LessThan(int A, int B) { return A<b; }

	private static string Partition(int[] List, int M, int Position, Function Compare) {
		if(Position>=List.Length) return "";
		else return ((Compare(List[Position], M))?List[Position].ToString():"") + Partition(List, M, Position+1, Compare);
	}
	private static bool Median(int[] List, int M) {
		return (Partition(List, M, 0, new Function(GreaterThan)).Length == Partition(List, M, 0, new Function(LessThan)).Length);
	}
	public static void Main(string [] args ){
		Console.WriteLine("Is Median: {0}", (Median(new int[]{1,2,3,4,5,6,7}, 4)?"Yes":"No")); 
	}
} 
```

The most obvious way of solving this problem is to sort the list of numbers, go the middle of the sorted list and check to see weather List\[Middle\] == M. However, that's a little overkill. Why sort when all we need to do is check if a number a list's median? The method I used partitioned the list into two subsets. The first subset contains all numbers less than M and the second subset contains all numbers greater than M. If M is the median of the list then the first subset's length should equal the second subsets length. SIMPLE!

There are three main group of functions. The first function Median(int\[\], int) is the function that makes the call to Partition with proper arguments. Partition accepts the List, the median value M, the starting Position and finally and comparison delegate (function).

Notice how, Median makes two identical calls to Partition one with a GreaterThan delegate and the other with a LessThan delegate. If both this Partition calls return the same length string then M is the Median!