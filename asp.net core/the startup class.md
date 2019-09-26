# The Startup class

Date: 17.09.2019 
[Original article](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/startup?view=aspnetcore-2.2)  

.NET Core 2.x version of framework

Class where:  

- Services required by the app are configured.  
- The request handling pipeline is defined

```C#
public class Startup
{
    // Class constructor
    public Startup()
    {    
    }

    // Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
    }

    // Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app)
    {
    }
}
```

From the very beginning there are available the following classes throught dependency injection.  

- [IHostingEnvironment](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) to configure services by environment.  
- [IConfiguration](https://docs.microsoft.com/dotnet/api/microsoft.extensions.configuration.iconfiguration) to read configuration.  
- [ILoggerFactory](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.iloggerfactory) to create a logger in `Startup.ConfigureServices`.

```C#
public class Startup
{
    private readonly IHostingEnvironment _env;
    private readonly IConfiguration _config;
    private readonly ILoggerFactory _loggerFactory;

    public Startup(IHostingEnvironment env, IConfiguration config, 
        ILoggerFactory loggerFactory)
    {
        _env = env;
        _config = config;
        _loggerFactory = loggerFactory;
    }
}
```

## Multiple Startup

Can be used names, specific for environments:

- StartupDevelopment
- StartupProduction
- etc

## The ConfigureServices method

For features that require substantial setup, there are `Add{Service}` extension methods on [IServiceCollection](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection). For example, **Add**DbContext, **Add**DefaultIdentity, **Add**EntityFrameworkStores, and **Add**RazorPages

```C#
public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<IMyDependency, MyDependency>();
    services.AddTransient<IOperationTransient, Operation>();
    services.AddScoped<IOperationScoped, Operation>();
    services.AddSingleton<IOperationSingleton, Operation>();
    services.AddSingleton<IOperationSingletonInstance>(new Operation(Guid.Empty));

    // OperationService depends on each of the other Operation types.
    services.AddTransient<OperationService, OperationService>();
}
```

[Service lifetimes](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-2.2#service-lifetimes) are described later in this topic.

## The Configure method

The [Configure](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) method is used to specify how the app responds to HTTP requests. The request pipeline is configured by adding [middleware](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/middleware/index?view=aspnetcore-2.2) components to an [IApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder) instance.

```C#
public void Configure(IApplicationBuilder app)
{
    app.UseMiddleware<MyMiddleware>();
}
```

## Configure services without Startup

```C#
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        WebHost.CreateDefaultBuilder(args)
            .ConfigureServices(services =>
            {
                ...
            })
            .Configure(app =>
            {                
                ...
            });
    }
}
```

## Extend Startup with startup filters

See [section](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/startup?view=aspnetcore-2.2#extend-startup-with-startup-filters) in Microsoft documentation
