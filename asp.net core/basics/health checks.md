# Health checks
Date: 31.10.2019  
[Original](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/health-checks?view=aspnetcore-3.0)  

.NET version: 3.x  

## Packages
- Microsoft.AspNetCore.Diagnostics.HealthChecks
- Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore
- AspNetCore.Diagnostics.HealthChecks
- AspNetCore.HealthChecks.SqlServer

## Basic setup
``` csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

## Create health checks
``` csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("An unhealthy result."));
    }
}
```

## Register health check services
``` csharp
services.AddHealthChecks().AddCheck<ExampleHealthCheck>("example_health_check");
```

## Use Health Checks Routing
``` csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});

```

## Require host
```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

## Require authorization
``` csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

## Filter health checks
```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

``` csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

## Customize the HTTP status code
```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

## Suppress cache headers
```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

## Customize output
```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

## Database probe
``` csharp
services.AddHealthChecks().AddSqlServer(Configuration["ConnectionStrings:DefaultConnection"]);
```

## Entity Framework Core DbContext probe
```csharp
services.AddHealthChecks().AddDbContextCheck<AppDbContext>();
```

## Separate readiness and liveness probes
``` csharp
public class StartupHostedServiceHealthCheck : IHealthCheck
{
    private volatile bool _startupTaskCompleted = false;

    public string Name => "slow_dependency_check";

    public bool StartupTaskCompleted
    {
        get => _startupTaskCompleted;
        set => _startupTaskCompleted = value;
    }

    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context, 
        CancellationToken cancellationToken = default(CancellationToken))
    {
        if (StartupTaskCompleted)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The startup task is finished."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The startup task is still running."));
    }
}
```

```csharp
public class StartupHostedService : IHostedService, IDisposable
{
    private readonly int _delaySeconds = 15;
    private readonly ILogger _logger;
    private readonly StartupHostedServiceHealthCheck _startupHostedServiceHealthCheck;

    public StartupHostedService(ILogger<StartupHostedService> logger, 
        StartupHostedServiceHealthCheck startupHostedServiceHealthCheck)
    {
        _logger = logger;
        _startupHostedServiceHealthCheck = startupHostedServiceHealthCheck;
    }

    public Task StartAsync(CancellationToken cancellationToken)
    {
        _logger.LogInformation("Startup Background Service is starting.");

        // Simulate the effect of a long-running startup task.
        Task.Run(async () =>
        {
            await Task.Delay(_delaySeconds * 1000);

            _startupHostedServiceHealthCheck.StartupTaskCompleted = true;

            _logger.LogInformation("Startup Background Service has started.");
        });

        return Task.CompletedTask;
    }

    public Task StopAsync(CancellationToken cancellationToken)
    {
        _logger.LogInformation("Startup Background Service is stopping.");

        return Task.CompletedTask;
    }

    public void Dispose()
    {
    }
}
```

``` csharp
services.AddHostedService<StartupHostedService>();
services.AddSingleton<StartupHostedServiceHealthCheck>();

services.AddHealthChecks()
    .AddCheck<StartupHostedServiceHealthCheck>(
        "hosted_service_startup", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "ready" });

services.Configure<HealthCheckPublisherOptions>(options =>
{
    options.Delay = TimeSpan.FromSeconds(2);
    options.Predicate = (check) => check.Tags.Contains("ready");
});

services.AddSingleton<IHealthCheckPublisher, ReadinessPublisher>();
```

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

## Metric-based probe with a custom response writer
``` csharp
public class MemoryHealthCheck : IHealthCheck
{
    private readonly IOptionsMonitor<MemoryCheckOptions> _options;

    public MemoryHealthCheck(IOptionsMonitor<MemoryCheckOptions> options)
    {
        _options = options;
    }

    public string Name => "memory_check";

    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context, 
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var options = _options.Get(context.Registration.Name);

        // Include GC information in the reported diagnostics.
        var allocated = GC.GetTotalMemory(forceFullCollection: false);
        var data = new Dictionary<string, object>()
        {
            { "AllocatedBytes", allocated },
            { "Gen0Collections", GC.CollectionCount(0) },
            { "Gen1Collections", GC.CollectionCount(1) },
            { "Gen2Collections", GC.CollectionCount(2) },
        };

        return Task.FromResult(new HealthCheckResult(
            context.Registration.FailureStatus,
            description: "Reports degraded status if allocated bytes " +
                $">= {options.Threshold} bytes.",
            exception: null,
            data: data));
    }
}
```

```csharp
services.AddHealthChecks().AddMemoryHealthCheck("memory");
```

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

```csharp
private static Task WriteResponse(HttpContext httpContext, 
    HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

## Filter by port
[link](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/health-checks?view=aspnetcore-3.0#filter-by-port)

## Distribute a health check library
``` csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

namespace SampleApp
{
    public class ExampleHealthCheck : IHealthCheck
    {
        private readonly string _data1;
        private readonly int? _data2;

        public ExampleHealthCheck(string data1, int? data2)
        {
            _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
            _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
        }

        public async Task<HealthCheckResult> CheckHealthAsync(
            HealthCheckContext context, CancellationToken cancellationToken)
        {
            try
            {
                return HealthCheckResult.Healthy();
            }
            catch (AccessViolationException ex)
            {
                return new HealthCheckResult(
                    context.Registration.FailureStatus,
                    description: "An access violation occurred during the check.",
                    exception: ex,
                    data: null);
            }
        }
    }
}
```

``` csharp
using System.Collections.Generic;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public static class ExampleHealthCheckBuilderExtensions
{
    const string DefaultName = "example_health_check";

    public static IHealthChecksBuilder AddExampleHealthCheck(
        this IHealthChecksBuilder builder,
        string name = default,
        string data1,
        int data2 = 1,
        HealthStatus? failureStatus = default,
        IEnumerable<string> tags = default)
    {
        return builder.Add(new HealthCheckRegistration(
            name ?? DefaultName,
            sp => new ExampleHealthCheck(data1, data2),
            failureStatus,
            tags));
    }
}
```

## Health Check Publisher
``` csharp
public class ReadinessPublisher : IHealthCheckPublisher
{
    private readonly ILogger _logger;

    public ReadinessPublisher(ILogger<ReadinessPublisher> logger)
    {
        _logger = logger;
    }

    // The following example is for demonstration purposes only. Health Checks 
    // Middleware already logs health checks results. A real-world readiness 
    // check in a production app might perform a set of more expensive or 
    // time-consuming checks to determine if other resources are responding 
    // properly.
    public Task PublishAsync(HealthReport report, 
        CancellationToken cancellationToken)
    {
        if (report.Status == HealthStatus.Healthy)
        {
            _logger.LogInformation("{Timestamp} Readiness Probe Status: {Result}", 
                DateTime.UtcNow, report.Status);
        }
        else
        {
            _logger.LogError("{Timestamp} Readiness Probe Status: {Result}", 
                DateTime.UtcNow, report.Status);
        }

        cancellationToken.ThrowIfCancellationRequested();

        return Task.CompletedTask;
    }
}
```

``` csharp
services.AddHostedService<StartupHostedService>();
services.AddSingleton<StartupHostedServiceHealthCheck>();

services.AddHealthChecks()
    .AddCheck<StartupHostedServiceHealthCheck>(
        "hosted_service_startup", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "ready" });

services.Configure<HealthCheckPublisherOptions>(options =>
{
    options.Delay = TimeSpan.FromSeconds(2);
    options.Predicate = (check) => check.Tags.Contains("ready");
});

services.AddSingleton<IHealthCheckPublisher, ReadinessPublisher>();
```

## Restrict health checks with MapWhen
[link](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/health-checks?view=aspnetcore-3.0#restrict-health-checks-with-mapwhen)









































