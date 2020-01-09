# Logging
Date: 02.10.2019  
[Original](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging/?view=aspnetcore-3.0)  

## Providers
- Console
- Debug
- EventSource
- EventLog
- TraceSource
- AzureAppServicesFile
- AzureAppServicesBlob
- ApplicationInsights
- elmah.io ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
- Gelf ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))
- JSNLog ([GitHub repo](https://github.com/mperdeck/jsnlog))
- KissLog.net ([GitHub repo](https://github.com/catalingavan/KissLog-net))
- Loggr ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))
- NLog ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))
- Sentry ([GitHub repo](https://github.com/getsentry/sentry-dotnet))
- Serilog ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))
- Stackdriver ([Github repo](https://github.com/googleapis/google-cloud-dotnet))

## Add providers
``` csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {
            logging.ClearProviders();
            logging.AddConsole();
        });
```

``` csharp
var loggerFactory = LoggerFactory.Create(builder =>
{
    builder
        .AddFilter("Microsoft", LogLevel.Warning)
        .AddFilter("System", LogLevel.Warning)
        .AddFilter("LoggingConsoleApp.Program", LogLevel.Debug)
        .AddConsole()
        .AddEventLog();
});

ILogger logger = loggerFactory.CreateLogger<Program>();
logger.LogInformation("Example log message");
```

## Using
``` csharp
public class AboutModel : PageModel
{
    private readonly ILogger _logger;

    public AboutModel(ILogger<AboutModel> logger)
    {
        _logger = logger;
    }
    
    public void OnGet()
    {
        Message = $"About page visited at {DateTime.UtcNow.ToLongTimeString()}";
        _logger.LogInformation("Message displayed: {Message}", Message);
    }    
```

``` csharp
public static void Main(string[] args)
{
    var logger = host.Services.GetRequiredService<ILogger<Program>>();
    logger.LogInformation("Seeded the database.");
}
```

``` csharp
public void Configure(IApplicationBuilder app, IHostEnvironment env, ILogger<Startup> logger)
{
    if (env.IsDevelopment())
    {
        logger.LogInformation("In Development environment");
        app.UseDeveloperExceptionPage();
    }
}
```

## Configuration
[link](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging/?view=aspnetcore-3.0#configuration)
























