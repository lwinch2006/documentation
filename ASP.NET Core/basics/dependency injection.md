# Dependency injection

Date: 17.09.2019 
[Original article](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-2.2)  

Version: .NET Core 2.x  

## Content

[TOC]  

Dependency injection addresses these problems through:

- The use of an interface or base class to abstract the dependency implementation.
- Registration of the dependency in a service container. ASP.NET Core provides a built-in service container, [IServiceProvider](https://docs.microsoft.com/dotnet/api/system.iserviceprovider). Services are registered in the app's `Startup.ConfigureServices` method.
- _Injection_ of the service into the constructor of the class where it's used. The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.

Creating interface of the dependency.

```c#
public interface IMyDependency
{
    Task WriteMessage(string message);
}
```



Creating implementation of the dependency:

```c#
public class MyDependency : IMyDependency
{
    private readonly ILogger<MyDependency> _logger;

    public MyDependency(ILogger<MyDependency> logger)
    {
        _logger = logger;
    }

    public Task WriteMessage(string message)
    {
        _logger.LogInformation(
            "MyDependency.WriteMessage called. Message: {MESSAGE}", 
            message);

        return Task.FromResult(0);
    }
}
```



Registering dependency in the ConfigureServices() function

```C#
public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<IMyDependency, MyDependency>();
}
```



Using dependency in the user class

```C#
public class IndexModel : PageModel
{
    private readonly IMyDependency _myDependency;

    public IndexModel(
        IMyDependency myDependency)
    {
        _myDependency = myDependency;
    }

    public async Task OnGetAsync()
    {
        await _myDependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```





## Service lifetimes

### Transient

Transient lifetime services ([AddTransient](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions.addtransient)) are created each time they're requested from the service container. This lifetime works best for lightweight, stateless services.



### Scoped

Scoped lifetime services ([AddScoped](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions.addscoped)) are created once per client request (connection).



### Singleton

Singleton lifetime services ([AddSingleton](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions.addsingleton)) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration). Every subsequent request uses the same instance.



## Service registration methods

| Method                                                       | Automatic object disposal | Multiple implementations | Pass args |
| :----------------------------------------------------------- | :-----------------------: | :----------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()` Example: `services.AddScoped<IMyDep, MyDep>();` |            Yes            |           Yes            |    No     |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})` Examples: `services.AddScoped<IMyDep>(sp => new MyDep());` `services.AddScoped<IMyDep>(sp => new MyDep("A string!"));` |            Yes            |           Yes            |    Yes    |
| `Add{LIFETIME}<{IMPLEMENTATION}>()` Example: `services.AddScoped<MyDep>();` |            Yes            |            No            |    No     |
| `Add{LIFETIME}<{SERVICE}>(new {IMPLEMENTATION})` Examples: `services.AddScoped<IMyDep>(new MyDep());` `services.AddScoped<IMyDep>(new MyDep("A string!"));` |            No             |           Yes            |    Yes    |
| `Add{LIFETIME}(new {IMPLEMENTATION})` Examples: `services.AddScoped(new MyDep());` `services.AddScoped(new MyDep("A string!"));` |            No             |            No            |    Yes    |



`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.

```C#
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

For more information, see:

- [TryAdd](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.extensions.servicecollectiondescriptorextensions.tryadd)
- [TryAddTransient](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.extensions.servicecollectiondescriptorextensions.tryaddtransient)
- [TryAddScoped](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.extensions.servicecollectiondescriptorextensions.tryaddscoped)
- [TryAddSingleton](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.extensions.servicecollectiondescriptorextensions.tryaddsingleton)



[TryAddEnumerable(ServiceDescriptor)](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.extensions.servicecollectiondescriptorextensions.tryaddenumerable) methods only register the service if there isn't already an implementation *of the same type*. Multiple services are resolved via `IEnumerable<{SERVICE}>`. 

```C#
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```



### Constructor injection behavior

Services can be resolved by two mechanisms:

- [IServiceProvider](https://docs.microsoft.com/dotnet/api/system.iserviceprovider)
- [ActivatorUtilities](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.activatorutilities) 



## Call services from main

```C#
public static void Main(string[] args)
{
    var host = CreateWebHostBuilder(args).Build();

    using (var serviceScope = host.Services.CreateScope())
    {
        var services = serviceScope.ServiceProvider;

        try
        {
            var serviceContext = services.GetRequiredService<MyScopedService>();
            // Use the context here
        }
        catch (Exception ex)
        {
            var logger = services.GetRequiredService<ILogger<Program>>();
            logger.LogError(ex, "An error occurred.");
        }
    }

    host.Run();
}
```











