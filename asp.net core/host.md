# Host

Date: 23.09.2019

[Original](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/middleware/?view=aspnetcore-2.2)

.NET Core 2.x framework version



## Content

[TOC]

## Generic host

Generic Host is used for apps that don't process HTTP requests.

### Introduction

[IHostedService](https://docs.microsoft.com/dotnet/api/microsoft.extensions.hosting.ihostedservice) is the entry point to code execution. Each [IHostedService](https://docs.microsoft.com/dotnet/api/microsoft.extensions.hosting.ihostedservice) implementation is executed in the order of [service registration in ConfigureServices](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-2.2#configureservices). [StartAsync](https://docs.microsoft.com/dotnet/api/microsoft.extensions.hosting.ihostedservice.startasync) is called on each [IHostedService](https://docs.microsoft.com/dotnet/api/microsoft.extensions.hosting.ihostedservice) when the host starts, and [StopAsync](https://docs.microsoft.com/dotnet/api/microsoft.extensions.hosting.ihostedservice.stopasync) is called in reverse registration order when the host shuts down gracefully.



### Set up a host

```C#
public static async Task Main(string[] args)
{
    var host = new HostBuilder()
        .Build();

    await host.RunAsync();
}
```



### Options

[HostOptions](https://docs.microsoft.com/dotnet/api/microsoft.extensions.hosting.hostoptions) configure options for the [IHost](https://docs.microsoft.com/dotnet/api/microsoft.extensions.hosting.ihost).

```C#
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```



### Default services

- [Environment](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/environments?view=aspnetcore-2.2) ([IHostingEnvironment](https://docs.microsoft.com/dotnet/api/microsoft.extensions.hosting.ihostingenvironment))
- [HostBuilderContext](https://docs.microsoft.com/dotnet/api/microsoft.extensions.hosting.hostbuildercontext)
- [Configuration](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/index?view=aspnetcore-2.2) ([IConfiguration](https://docs.microsoft.com/dotnet/api/microsoft.extensions.configuration.iconfiguration))
- [IApplicationLifetime](https://docs.microsoft.com/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime) ([ApplicationLifetime](https://docs.microsoft.com/dotnet/api/microsoft.extensions.hosting.internal.applicationlifetime))
- [IHostLifetime](https://docs.microsoft.com/dotnet/api/microsoft.extensions.hosting.ihostlifetime) ([ConsoleLifetime](https://docs.microsoft.com/dotnet/api/microsoft.extensions.hosting.internal.consolelifetime))
- [IHost](https://docs.microsoft.com/dotnet/api/microsoft.extensions.hosting.ihost)
- [Options](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-2.2) ([AddOptions](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.addoptions))
- [Logging](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging/index?view=aspnetcore-2.2) ([AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging))



### ConfigureHostConfiguration

```C#
var host = new HostBuilder()
    .ConfigureHostConfiguration(configHost =>
    {
        configHost.SetBasePath(Directory.GetCurrentDirectory());
        configHost.AddJsonFile("hostsettings.json", optional: true);
        configHost.AddEnvironmentVariables(prefix: "PREFIX_");
        configHost.AddCommandLine(args);
    })
```



### ConfigureAppConfiguration

```C#
var host = new HostBuilder()
    .ConfigureAppConfiguration((hostContext, configApp) =>
    {
        configApp.SetBasePath(Directory.GetCurrentDirectory());
        configApp.AddJsonFile("appsettings.json", optional: true);
        configApp.AddJsonFile(
            $"appsettings.{hostContext.HostingEnvironment.EnvironmentName}.json", 
            optional: true);
        configApp.AddEnvironmentVariables(prefix: "PREFIX_");
        configApp.AddCommandLine(args);
    })
```



### ConfigureHostConfiguration VS ConfigureAppConfiguration

Host config is for:

- App name
- Environment
- Content root
- Kestrel



For everything else is app config.



### ConfigureServices

[ConfigureServices](https://docs.microsoft.com/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices) adds services to the app's [dependency injection](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-2.2) container.



### ConfiggureLogging

```C#
var host = new HostBuilder()
    .ConfigureLogging((hostContext, configLogging) =>
    {
        configLogging.AddConsole();
        configLogging.AddDebug();
    })
```



### Container configuration

Read more ([link](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-2.2#container-configuration))



### Extensibility

Usage of extension

```C#
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();
```



Implementation of extension

```C#
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```



## Web Host

Web Host is for hosting web apps.



## Set up a host

```C#
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object. This separation is required if you use [Entity Framework Core tools](https://docs.microsoft.com/en-us/ef/core/miscellaneous/cli/). The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app. An alternative is to implement `IDesignTimeDbContextFactory`.





























