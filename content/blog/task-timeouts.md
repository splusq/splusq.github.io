---
title: 'Task Timeouts'
date: Tue, 26 Feb 2013 03:25:30 +0000
draft: false
tags: ['C#', '.NET']
---

When dealing with .NET tasks – we often want to timeout the operation if it does not complete within a certain period of time. For instance, if you make an asynchronous WebRequest call – you may want to timeout this asynchronous operation within 3 seconds if you don’t get a response back. In the past, we had to create Timers to monitor the Task (or the Thread) for completion in the callback of the timer, and then do other crazy gymnastics to make it all play well together. However, the result was a code that was extremely difficult to understand because you had these disparate methods, without any cohesiveness within the code. With .NET 4.5 we can simplify this dramatically.

```csharp
public static async Task<T> TimeoutAfter<T>(this Task<T> task, int delay)
{
	await Task.WhenAny(task, Task.Delay(delay));

	if (!task.IsCompleted)
	{
		throw new TimeoutException();	
	}

	return await task;
} 
```

The idea is to use the WhenAny method, which creates a Task that completes when either one of the task completes. In this case, we pass in the Task that we want to monitor (task) and we pass a Task.Delay task which is yet another task that completes after a specified amount of milliseconds. So either the Task.Delay will complete or our task will complete. Simple, and elegant. And the best part is this is just an extension method so you can use it anywhere! You can even compose them into a chain of other extension methods.