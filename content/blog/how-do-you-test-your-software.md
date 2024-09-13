---
title: 'How do you test your software?'
date: Fri, 01 Mar 2013 15:09:40 +0000
draft: false
tags: ['Uncategorized']
---

There seems to be two types of developers out there. One that believes in integration tests, and the other that believes in both unit and integration tests. Integration tests are tests that execute everything as if they were real "transactions" only to verify the expected results. For instance, if you were building a web application that managed a list of employees and their supervisors. Your integration test would probably add an employee to the database (actually add it), and then read it back from that database, and so on (from a test database of course). There's nothing wrong this, and in fact I would argue these types of tests are great. You need to write integration tests that exercise your entire stack. Without it would be like exercising without doing cardio. What differentiates a unit test from other types of tests is that you test a "unit" of your application alone. Now what that unit is, is up to you. But the point is you take a _part_ of your application and test it in _isolation_. That part could be a class, it could be multiple sets of classes, but not your application as a whole - otherwise it would become an integration test. Seems like we have a conundrum here. We just argued that a integration test, tests everything, end-to-end, the entire stack -- and a unit test, tests a part of the application in isolation. Isn't integration test then a superset of unit tests? And there is where the confusion lies. **So why write unit tests?** While it is true that unit tests can often uncover bugs in the code, in reality unit tests actually help discover a far bigger problem; and that is design of your system. Unit tests can discover design issues if left unaddressed could severely impact your velocity to adapt to change. I would argue that if you can write proper tests for your system with good code coverage, then you have a well designed, cohesive system. Now unit testing alone is no silver bullet to design verification, but it's pretty close. Let's look at a really simple example:

```csharp
public class PaymentManager {
	private Logger logger;

	public PaymentManager() {
		this.logger = new InstrumentationLogger();
	}

	public void Credit(Transaction transaction) {
		// credit 
		this.logger.Log("Credit Amount ", transaction.Amount);
	}
} 
```

Now this looks like a perfectly valid code. Would pass all sorts of integration tests, but there's a glaring problem here. The problem has to do with the Logger. At this point it should be obvious to anyone reading this that where the Logger logs this message is irrelevant to the PaymentManager. It could care less. The logger could decide to log to a database, to the file system, to a message queue, to the cloud. Who knows. Doesn't matter. However, the very fact that we have the initialization of the logger is in the constructor - we actually coupled the PaymentManager to how the logger functions, weather we like it or not. Think for a second why that is. If the Logger was to log to a database, then it's very likely in the constructor of the InstrumentationLogger we would be initializing a connection to a database preparing ourselves to log messages - which means we would need a connection string, and all sorts of other stuff available in the "context" of the PaymentManager. Now think for a second what happens when you want to write a test for this class PaymentManager. It will indirectly need all the database settings, a "test" database just to be able to test this class. Which doesn't make any sense, because what you are trying to test is the PaymentManager not the Logger. But the way this class is written there's really not much you can do. This is where a unit test helps you. When you struggle to write tests for something that generally means you have a big design problem. So let's see how we can fix this:

```csharp
public class PaymentManager {
	private ILogger logger;

	public PaymentManager(ILogger logger) {
		this.logger = logger;
	}

	public void Credit(Transaction transaction) {
		// credit 
		this.logger.Log("Credit Amount ", transaction.Amount);
	}
} 
```

So we made two subtle changes. First we changed Logger to a more abstract interface ILogger, and secondly instead of constructing the logger we made it [someone elses' responsibility](http://en.wikipedia.org/wiki/Inversion_of_control). Now when we are testing this we can easily pass a fake object that implements ILogger and we can test our `PaymentManager`. Now that `PaymentManager` depends on a more abstract concept a `ILogger` - we can replace it with anything that implements that interface. We have [externalized the details of our dependency](http://en.wikipedia.org/wiki/Abstraction_principle_(computer_programming)). This leads to patterns like [Inversion of Control](http://en.wikipedia.org/wiki/Inversion_of_control), and [dependency injection](http://en.wikipedia.org/wiki/Dependency_injection). I will cover these topics in-depth as well. So I hope with this very simple it was clear to you that unit test, not only identifies bugs in your code, it can also help identify design issues.