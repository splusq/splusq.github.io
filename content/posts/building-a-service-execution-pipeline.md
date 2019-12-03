---
title: 'Building a service execution pipeline'
date: Mon, 08 Apr 2013 05:19:13 +0000
draft: false
tags: ['Uncategorized']
---

Most software built today have a notion of a client and a service. This is even more true with mobile/web applications because you want your client apps to do as little as possible, and your service to do most of the heavy lifting. This allows you to improve your service without requiring constant client updates. Also since you have a single service that serves potentially various native clients (iOS, Android, or Windows Phone), being able to update it independently of your users gives you a clear competitive edge. So today I want to focus on building a service based on this notion of a "pipeline". Most .NET client/service frameworks already have this concept. For instance, Windows Communication Foundation (WCF) has an execution pipeline, and so does ASP.NET. This allows you to extend the behavior of these frameworks at various points during the execution. But that's not really what I want to talk about today. I want to talk about how _you can build_ your services such that you can add/remove functionality using a pipeline-style execution. As we are talking about the implementation of this pipeline I also want to take this opportunity to discuss good software design practices, and it's uses.

### Motivations

**Building distinct components that perform a very specific functionality and nothing more** If you don't build services this way, our natural inclination as developers, will be to pile on top of the code that already exists. Think about authentication for example, your service may do user authentication against a local database but you want to add support for Facebook, or Twitter. Assuming that you have a component that's responsible for doing the authentication with a local database today, when you want to support these additional parties, the immediate thought is to update this authentication logic to support it. The problem with this is you simply cannot tease apart facebook, from twitter from your local authentication. They are all in one bucket. Either you have them all or you don't. And the same can be said about many other types of functionality within your system. **Being able to enable/disable functionality of your service without code change** One of key decisions you need to make when building a service is to recognize the inevitable truth that dependencies of your services will break down. Period. If you program knowing this fact, then you are more likely to build resiliant systems that can withstand these dramatic erruptions. One of the key ways to do this is to design your services so that they can deteriorate in terms of performance or functionality instead of completely breaking down. The only way to do this properly is if you can identify and isolate specific functionalities in your system and have the ability to enable/disable them. Like we said earlier if all your functionalities are burried in a bucket of water, you've already muddied it - there's nothing you can do when a bad drop of oil is poured in. But if each arbitrary subset of water droplets were contained in a packet, and you could identify which was the bad breed, you could easily remove it from the container and let the rest of them continue to function.

### Solution

So the way to solve this problem is to build on somewhat of a modified [chain-of-responsibility principle](http://en.wikipedia.org/wiki/Chain-of-responsibility_pattern). Where each item in the chain is handling a single responsibility and then forwarding the request over to the next party in the chain and the execution continues. Theoratically every non-shared chunk of code can become an item in the chain but then it becomes difficult to stich them together. So the right balance is to isolate a feature in an item, and then string together the chain to build the overall functionality. But we are sort of getting ahead of ourselves. In order to build a fully functional high performance, execution pipeline, we'll need to build many foundational pieces. So instead of doing that, today we will start with a simple, synchronous, one-way, non-hierarchial execution pipeline. Let's get started with a simple console application:

```csharp
static void Main(string[] args)
{
    // configure the MEF container
    using (var container = ConfigureMef())
    {
        // create a pipeline flow - logging/fake response/terminate
        IPipeline pipeline = new LoggingPipeline(new FakeResponsePipeline());

        // inject the property
        container.ComposeExportedValue<IPipeline>(pipeline);

        // resolve the root type - a simple http server
        var server = container.GetExportedValue<Server>();

        // start the server
        var service = server.Start(@"http://127.0.0.1:8080/");

        // keep accepting connections
        service.Wait();
    } // dispose
} 
```

First notice that I am using the [Managed Extensibility Framework](http://mef.codeplex.com/) as my [DI container](http://en.wikipedia.org/wiki/Dependency_injection). Any other [DI container](http://www.hanselman.com/blog/ListOfNETDependencyInjectionContainersIOC.aspx) would work in this case, but MEF allowed me to stick with the .NET framework and also didn't need any additional configuration to work - which was nice for this simple example. So the first thing to notice is that we have an interface for our Pipeline which we are preparing externally from the actual service. In this case we are saying that the pipeline will consist of Logging and a FakeResponse - and injecting that to our server. Our server then will execute the pipeline following the chain. It's easy to see how we can externalize this configuration to a config file allowing us to modify the behavior of the service without necessarily making a code change. We will talk more about this just a little later, for now let's look at how the server is configured:

```csharp
[Export]
public class Server
{
    private readonly IHttpListener listener;
    private readonly IPipeline pipeline;

    [ImportingConstructor]
    public Server(IHttpListener listener, IPipeline pipeline)
    {
        this.listener = listener;
        this.pipeline = pipeline;
    }

    public async Task Start(string address)
    {
        this.listener.Prefixes.Add(address);
        this.listener.Start();

        // keep listening
        while (true)
        {
            // wait for a listener
            var context = await this.listener.GetContextAsync().ConfigureAwait(false);

            // initiate the pipeline and forget
            this.pipeline.Continue(context);
        }
    }
} 
```

This is probably one of the most simplest HTTP servers. It accepts a requests and passes it forward to the first item in the pipeline. That's it. Recall, the pipeline was built external to the server and injected to it.

```csharp
[InheritedExport]
public interface IPipeline
{
    void Continue(HttpListenerContext listenerContext);
} 
```

The execution pipeline interface is extremely simple. It accepts a Continuation to the next execution pipeline. One of things you want to do when you build interfaces is to think of the minimal set that satifies what you are trying to do. The leaner your interfaces, the less likely they will change, and therefore have less impact on the overall system. The recommendaton for most interfaces is to have no more than 3-4 methods. The .NET Framework interfaces not surprisingly has 3.75 members, with a methods-to-properties ratio of 3.5:1. If your interfaces start to have more than 10 methods, you're probably building more than one responsiblity in an interface and there's probably an opportunity to separate them. This task is often referred to as decomposition. Now that we have the base interface we need to build an abstract concept that allows to move to the next item in the pipeline. Because interface does not define that, the interface just says you should be able to continue. For that we create an abstract BaseContinuationPipeline.

```csharp
public abstract class BaseContinuationPipeline : IPipeline
{
    private IPipeline forward;

    public BaseContinuationPipeline()
    {
        // default is the terminating pipeline
        this.forward = new TerminatingPipeline(); 
    }
    
    public BaseContinuationPipeline(IPipeline forward)
    {
        this.forward = forward;
    }

    public virtual void Continue(HttpListenerContext listenerContext)
    {
        Task.Run(() =>
        {
            // execute the "abstract" action
            this.Execute(listenerContext);

            // continue to the next action
            this.forward.Continue(listenerContext);
        });
    }

    protected abstract void Execute(HttpListenerContext listenerContext);
} 
```

There are few things to point out here: the first thing is the default forward continuation to the pipeline is this special item in the pipeline called a terminating pipeline. It's sole purpose is to end the request. This pattern of using a special object to handle empty set is referred to as the [Null Object Pattern](http://en.wikipedia.org/wiki/Null_Object_pattern). There were several ways to implement this special case. For instance, I could have simply left the forward object to null, and if it was null - prevent the forwarding and end the request. However, the problem with this is you are implementing a special case while your normal control flow of your logic can support it. The second thing is there might be other parts of the pipeline where I may decide to end the request, in which case I only need to forward it to the terminating pipeline (no repeating logic). One good sign of a well designed software is it's overall lack of conditional statements. If you think about it conditional statements (if/else, switch) are sort of a forced behavior to your normal control flow. Now I am not saying remove all conditional statements, that would be absurd there is no way to check if 5 > 3 without actually doing a if statement but for business objects you should think twice if you are constantly checking if the object is null. Also notice that the default is the terminating pipeline. This means even if the caller does not pass any feature sets to the pipeline the default behavior will be stop the execution. You always want to do a safe design, meaning that your APIs are full proof such that no matter how you interact with it the baseline behavior is at least functional. So let's keep moving forward. This abstract class does not know about any features so it simply describes how to move forward to the next continuation and keeps the execution abstract. The current item in the pipeline is executed by doing this.Execute, and then the request is forwarded to the next responsible party. That's it.

```csharp
public class TerminatingPipeline : IPipeline
{
    public void Continue(HttpListenerContext listenerContext)
    {
        // end the response stream
        listenerContext.Response.Close();

        // no more forwarding
    }
} 
```

So like we talked about before the terminating pipeline implements IPipeline interface and it's continuation is to simply to end the request and there's no where to forward to since this is always going to be the last item in the list. And to close, let's look at the two items in our pipeline, the logging and the fakeresponse:

```csharp
public class LoggingPipeline : BaseContinuationPipeline
{
    public LoggingPipeline(IPipeline forward) : base(forward) { }

    protected override void Execute(HttpListenerContext listenerContext)
    {
        Console.WriteLine(listenerContext.Request.Url);
    }
}

public class FakeResponsePipeline : BaseContinuationPipeline
{
    protected override void Execute(HttpListenerContext listenerContext)
    {
        string response = "hello world";
        listenerContext.Response.OutputStream.WriteAsync(ASCIIEncoding.ASCII.GetBytes(response), 0, response.Length);
    }
} 
```

They both implement the BaseContinuationPipeline because they both support continuations. In fact, all our pipeline elements will implement continuations except the special TerminatingPipeline. The loggig pipeline simply prints the incoming URL, and the FakeResponse writes Hello World to the output. With that we have a foundation to build something on top of. I will continue this discussion with more functionality with support for hierarchies, non-sequential and stateful pipelines. All of which will be required to build a fully functional service.