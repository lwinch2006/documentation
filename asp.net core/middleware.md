# Middleware

Date: 23.09.2019

[Original](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/middleware/?view=aspnetcore-2.2)

.NET Core 2.x framework version



Middleware is software that's assembled into an app pipeline to handle requests and responses. Each component:

- Chooses whether to pass the request to the next component in the pipeline.
- Can perform work before and after the next component in the pipeline.

Request delegates are configured using [Run](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.runextensions.run), [Map](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.mapextensions.map), and [Use](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.useextensions.use) extension methods.



## Create a middleware pipeline with IApplicationBuilder

![Request processing pattern showing a request arriving, processing through three middlewares, and the response leaving the app. Each middleware runs its logic and hands off the request to the next middleware at the next() statement. After the third middleware processes the request, the request passes back through the prior two middlewares in reverse order for additional processing after their next() statements before leaving the app as a response to the client.](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/middleware/index/_static/request-delegate-pipeline.png?view=aspnetcore-2.2)



## Examples

The first [Run](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.runextensions.run) delegate terminates the pipeline.

```C#
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Run(async context =>
        {
            await context.Response.WriteAsync("Hello, World!");
        });
    }
}
```



Chain multiple request delegates together with [Use](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.useextensions.use).

```C#
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            // Do work that doesn't write to the Response.
            await next.Invoke();
            // Do logging or other work that doesn't write to the Response.
        });

        app.Run(async context =>
        {
            await context.Response.WriteAsync("Hello from 2nd delegate.");
        });
    }
}
```



## Order

The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response. The order is critical for security, performance, and functionality.

```C#
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();
    app.UseMvc();
}
```



## Use, Run, and Map

- The `Use` method can short-circuit the pipeline
- `Run` runs at the end of the pipeline.
- `Map` extensions are used for branching the pipeline.

```C#
public class Startup
{
    private static void HandleMapTest1(IApplicationBuilder app)
    {
        app.Run(async context =>
        {
            await context.Response.WriteAsync("Map Test 1");
        });
    }

    private static void HandleMapTest2(IApplicationBuilder app)
    {
        app.Run(async context =>
        {
            await context.Response.WriteAsync("Map Test 2");
        });
    }

    public void Configure(IApplicationBuilder app)
    {
        app.Map("/map1", HandleMapTest1);

        app.Map("/map2", HandleMapTest2);

        app.Run(async context =>
        {
            await context.Response.WriteAsync("Hello from non-Map delegate. <p>");
        });
    }
}
```



- `MapWhen` branches the request pipeline based on the result of the given predicate.

```C#
public class Startup
{
    private static void HandleBranch(IApplicationBuilder app)
    {
        app.Run(async context =>
        {
            var branchVer = context.Request.Query["branch"];
            await context.Response.WriteAsync($"Branch used = {branchVer}");
        });
    }

    public void Configure(IApplicationBuilder app)
    {
        app.MapWhen(context => context.Request.Query.ContainsKey("branch"),
                               HandleBranch);

        app.Run(async context =>
        {
            await context.Response.WriteAsync("Hello from non-Map delegate. <p>");
        });
    }
}
```



`Map` supports nesting

```C#
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```



`Map` can also match multiple segments at once

```C#
public class Startup
{
    private static void HandleMultiSeg(IApplicationBuilder app)
    {
        app.Run(async context =>
        {
            await context.Response.WriteAsync("Map multiple segments.");
        });
    }

    public void Configure(IApplicationBuilder app)
    {
        app.Map("/map1/seg1", HandleMultiSeg);

        app.Run(async context =>
        {
            await context.Response.WriteAsync("Hello from non-Map delegate.");
        });
    }
}
```



## Built-in middleware

| Middleware                                                   | Description                                                  | Order                                                        |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Authentication](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity?view=aspnetcore-2.2) | Provides authentication support.                             | Before `HttpContext.User` is needed. Terminal for OAuth callbacks. |
| [Cookie Policy](https://docs.microsoft.com/en-us/aspnet/core/security/gdpr?view=aspnetcore-2.2) | Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`. | Before middleware that issues cookies. Examples: Authentication, Session, MVC (TempData). |
| [CORS](https://docs.microsoft.com/en-us/aspnet/core/security/cors?view=aspnetcore-2.2) | Configures Cross-Origin Resource Sharing.                    | Before components that use CORS.                             |
| [Diagnostics](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/error-handling?view=aspnetcore-2.2) | Several separate middlewares that provide a developer exception  page, exception handling, status code pages, and the default web page  for new apps. | Before components that generate errors. Terminal for exceptions or serving the default web page for new apps. |
| [Forwarded Headers](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/proxy-load-balancer?view=aspnetcore-2.2) | Forwards proxied headers onto the current request.           | Before components that consume the updated fields. Examples: scheme, host, client IP, method. |
| [Health Check](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/health-checks?view=aspnetcore-2.2) | Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability. | Terminal if a request matches a health check endpoint.       |
| [HTTP Method Override](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.httpmethodoverrideextensions) | Allows an incoming POST request to override the method.      | Before components that consume the updated method.           |
| [HTTPS Redirection](https://docs.microsoft.com/en-us/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.2#require-https) | Redirect all HTTP requests to HTTPS.                         | Before components that consume the URL.                      |
| [HTTP Strict Transport Security (HSTS)](https://docs.microsoft.com/en-us/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.2#http-strict-transport-security-protocol-hsts) | Security enhancement middleware that adds a special response header. | Before responses are sent and after components that modify requests. Examples: Forwarded Headers, URL Rewriting. |
| [MVC](https://docs.microsoft.com/en-us/aspnet/core/mvc/overview?view=aspnetcore-2.2) | Processes requests with MVC/Razor Pages.                     | Terminal if a request matches a route.                       |
| [OWIN](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/owin?view=aspnetcore-2.2) | Interop with OWIN-based apps, servers, and middleware.       | Terminal if the OWIN Middleware fully processes the request. |
| [Response Caching](https://docs.microsoft.com/en-us/aspnet/core/performance/caching/middleware?view=aspnetcore-2.2) | Provides support for caching responses.                      | Before components that require caching.                      |
| [Response Compression](https://docs.microsoft.com/en-us/aspnet/core/performance/response-compression?view=aspnetcore-2.2) | Provides support for compressing responses.                  | Before components that require compression.                  |
| [Request Localization](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/localization?view=aspnetcore-2.2) | Provides localization support.                               | Before localization sensitive components.                    |
| [Endpoint Routing](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/routing?view=aspnetcore-2.2) | Defines and constrains request routes.                       | Terminal for matching routes.                                |
| [Session](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/app-state?view=aspnetcore-2.2) | Provides support for managing user sessions.                 | Before components that require Session.                      |
| [Static Files](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/static-files?view=aspnetcore-2.2) | Provides support for serving static files and directory browsing. | Terminal if a request matches a file.                        |
| [URL Rewrite](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/url-rewriting?view=aspnetcore-2.2) | Provides support for rewriting URLs and redirecting requests. | Before components that consume the URL.                      |
| [WebSockets](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/websockets?view=aspnetcore-2.2) | Enables the WebSockets protocol.                             | Before components that are required to accept WebSocket requests. |

