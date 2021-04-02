# HttpClient infrastrucutre

## Class diagramm

![example](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.githubusercontent.com/AndreiGanichev/Student-Notes/main/HttpClient/HttpClient.puml)

## HttpClientFactoryOptions

```HttpClientFactoryOptions``` is named options(the name is HttpClient name).

It is configured in ConfigureService method:
```
    services.AddHttpClient("github", c =>
    {
        c.BaseAddress = new Uri("https://api.github.com/"); //configures HttpClient
    })
    .AddHttpMessageHandler<ValidateHeaderHandler>() //configures pipeline
    .AddHttpMessageHandler<RetryHandler>() //configures pipeline
    .AddHttpMessageHandler<LoggingHandler>() //configures pipeline

    .SetHandlerLifetime(TimeSpan.FromMinutes(3)); //configures pipeline lifetime
```

It contains a life time of ```ActiveHandlerTrackingEntry```. During this time every time you call ``CreateClient()``,
you will get a new instance of ``HttpClient``, but which has the same handler pipeline as was originally created.

It also configures pipeline handlers. The last added handler is the closest to an actual http request.

![pipeline](https://raw.githubusercontent.com/AndreiGanichev/Student-Notes/main/HttpClient/HttpClient_pipeline.svg)

## IHttpMessageHandlerBuilderFilter

```IHttpMessageHandlerBuilderFilter``` is directly analogous to the```IStartupFilters``` used by the ASP.NET Core middleware pipeline.There is one default: ```LoggingHttpMessageHandlerBuilderFilter```

## TimeOut

- ```HttpClient.Timeout``` will apply as an overall timeout to each entire operation through ```HttpClient```, including all tries and waits between retries.
- To apply a timeout-per-try, configure a ```RetryPolicy``` before a Polly ```TimeoutPolicy```.

## Interesting decisions
```ExpiredHandlerTrackingEntry``` has ```WeakReference``` to ```LifetimeTrackingHttpMessageHandler``` so it can track if the pipeline alive(not collected by GC after all HttpClients using the pipeline have been collected). If it is not alive the ```ExpiredHandlerTrackingEntry``` instance can also be disposed.

Active handlers(pipelines) are stored in ```DefaultHttpClientFactory``` field of type ```ConcurrentDictionary<string, Lazy<ActiveHandlerTrackingEntry>>```. Using Lazy<> here is a neat [trick](https://andrewlock.net/making-getoradd-on-concurrentdictionary-thread-safe-using-lazy/) to make the GetOrAdd function thread safe


## Sources
1. [Exploring the code behind IHttpClientFactory in depth](https://andrewlock.net/exporing-the-code-behind-ihttpclientfactory/) by Andrew Lock
1. [HTTPCLIENTFACTORY IN ASP.NET CORE 2.1 series](https://www.stevejgordon.co.uk/introduction-to-httpclientfactory-aspnetcore) by Steve Gordon
1. [DefaultHttpClientFactory.cs](https://github.com/aspnet/HttpClientFactory/blob/master/src/Microsoft.Extensions.Http/DefaultHttpClientFactory.cs)
1. [Use Case: Applying timeouts](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory#use-case-applying-timeouts)
1. [Better timeout handling with HttpClient](https://thomaslevesque.com/2018/02/25/better-timeout-handling-with-httpclient/)