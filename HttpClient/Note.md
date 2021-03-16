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
    .SetHandlerLifetime(TimeSpan.FromMinutes(3)); //configures pipeline lifetime
```

It contains a life time of ```ActiveHandlerTrackingEntry```. During this time every time you call ``CreateClient()``,
you will get a new instance of ``HttpClient``, but which has the same handler pipeline as was originally created.

## IHttpMessageHandlerBuilderFilter

```IHttpMessageHandlerBuilderFilter``` is directly analogous to the```IStartupFilters``` used by the ASP.NET Core middleware pipeline.There is one default: ```LoggingHttpMessageHandlerBuilderFilter```


## Sources
1. [Exploring the code behind IHttpClientFactory in depth](https://andrewlock.net/exporing-the-code-behind-ihttpclientfactory/) by Andrew Lock
1. [HTTPCLIENTFACTORY IN ASP.NET CORE 2.1 series](https://www.stevejgordon.co.uk/introduction-to-httpclientfactory-aspnetcore) by Steve Gordon
1. [DefaultHttpClientFactory.cs](https://github.com/aspnet/HttpClientFactory/blob/master/src/Microsoft.Extensions.Http/DefaultHttpClientFactory.cs)