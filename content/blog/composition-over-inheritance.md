---
title: 'Composition over Inheritance'
date: Sat, 09 Mar 2013 01:37:44 +0000
draft: false
tags: ['C#', 'Architecture']
---

There are several ways to extend the behavior of a class. One of my preferred approach to extending behaviors is composition, and that is what we are going to talk about today. Let's start with a simple example of inheritance:

```csharp
public class Search : ISearch { 
	IEnumerable<SearchResults> Search(Query q);
}

public class Related : Search {
	IEnumerable<RelatedEntities> Related(Query q);
} 
```

So in this simple example, we extend Search and create Related - which provides some additional functionality. But a completely different approach which provides same results is to use composition:

```csharp
public class Related : ISearch {
	private ISearch search;

	public IEnumerable<SearchResults> Search(Query q) {
		return this.search.Query(q);
	}
	public IEnumerable<RelatedEntities> Related(Query q) {
		// detail
	}
} 
```

Note that at least at this point functionally they are the same. However, with composition you have more control over what happens. For instance, with composition you can easily change how the Search behaves in your Related object - which you couldn't do in the previous option (unless your base method was virtual - which is something you may not be able to control). The reason this is called _composition_ is because you are essentially composing behavior from various different classes together to produce a new behavior. Think what happens if Related had other private objects that contributed towards a overall behavior - similar to chaining, comes the idea of composition. 

**When not to use composition?** 

Composition does not always work. For instance, if you need access to the protected fields of a class - you are tied to using inheritance to get to them. However, that does not mean that you cannot compromise. You may able to combine inheritance and composition. The main point I want to make is, think about composition over inheritance when you are making the decision to extend. I am sure you will find that more often than not composition will yield more open-ended designs.