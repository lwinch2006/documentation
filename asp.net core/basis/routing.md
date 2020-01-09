# Routing
Date: 08.10.2019  
[Original](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/routing?view=aspnetcore-3.0)  

## Use Routing Middleware
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRouting();
}
```

```csharp
var trackPackageRouteHandler = new RouteHandler(context =>
{
    var routeValues = context.GetRouteData().Values;
    return context.Response.WriteAsync(
        $"Hello! Route values: {string.Join(", ", routeValues)}");
});

var routeBuilder = new RouteBuilder(app, trackPackageRouteHandler);

routeBuilder.MapRoute(
    "Track Package Route",
    "package/{operation:regex(^track|create$)}/{id:int}");

routeBuilder.MapGet("hello/{name}", context =>
{
    var name = context.GetRouteValue("name");
    // The route handler when HTTP GET "hello/<anything>" matches
    // To match HTTP GET "hello/<anything>/<anything>, 
    // use routeBuilder.MapGet("hello/{*name}"
    return context.Response.WriteAsync($"Hi, {name}!");
});

var routes = routeBuilder.Build();
app.UseRouter(routes);
```

## URL generation reference
```csharp
app.Run(async (context) =>
{
    var dictionary = new RouteValueDictionary
    {
        { "operation", "create" },
        { "id", 123}
    };

    var vpc = new VirtualPathContext(context, null, dictionary, 
        "Track Package Route");
    var path = routes.GetVirtualPath(vpc).VirtualPath;

    context.Response.ContentType = "text/html";
    await context.Response.WriteAsync("Menu<hr/>");
    await context.Response.WriteAsync(
        $"<a href='{path}'>Create Package 123</a><br/>");
});
```










