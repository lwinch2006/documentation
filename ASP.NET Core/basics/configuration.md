# Configuration

Date: 26.09.2019

[Original](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.0)

.NET 3.x framework version



## Sources

- Azure Key Vault
- Azure App Configuration
- Command-line arguments
- Custom providers (installed or created)
- Directory files
- Environment variables
- In-memory .NET objects
- Settings files



## Host versus app configuration

- host configuration defines configuration for host 
- app configuration defines configuration for app
  - host configuration includes host configuration.



## Default configuration

The following applies to apps using the [Generic Host](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0). 

- Host configuration is provided from: 
  - Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.0#environment-variables-configuration-provider). The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.
  - Command-line arguments using the [Command-line Configuration Provider](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.0#command-line-configuration-provider).
- Web Host default configuration is established (`ConfigureWebHostDefaults`): 
  - Kestrel is used as the web server and configured using the app's configuration providers.
  - Add Host Filtering Middleware.
  - Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.
  - Enable IIS integration.
- App configuration is provided from: 
  - *appsettings.json* using the [File Configuration Provider](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.0#file-configuration-provider).
  - *appsettings.{Environment}.json* using the [File Configuration Provider](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.0#file-configuration-provider).
  - [Secret Manager](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-3.0) when the app runs in the `Development` environment using the entry assembly.
  - Environment variables using the [Environment Variables Configuration Provider](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.0#environment-variables-configuration-provider).
  - Command-line arguments using the [Command-line Configuration Provider](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.0#command-line-configuration-provider).



## Security

- [Use multiple environments in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/environments?view=aspnetcore-3.0)
- [Safe storage of app secrets in development in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-3.0)



## Conventions

### Keys

- Keys are case-insensitive. For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.
- If a value for the same key is set by the same or different  configuration providers, the last value set on the key is the value  used.
- Hierarchical keys 
  - Within the Configuration API, a colon separator (`:`) works on all platforms.
  - In environment variables, a colon separator may not work on all platforms. A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.
  - In Azure Key Vault, hierarchical keys use `--` (two  dashes) as a separator. You must provide code to replace the dashes with  a colon when the secrets are loaded into the app's configuration.
- The [ConfigurationBinder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.configuration.configurationbinder) supports binding arrays to objects using array indices in configuration keys. Array binding is described in the [Bind an array to a class](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.0#bind-an-array-to-a-class) section.



### Values

Configuration values adopt the following conventions:

- Values are strings.
- Null values can't be stored in configuration or bound to objects.



## Providers

| Provider                                                     | Provides configuration from…       |
| ------------------------------------------------------------ | ---------------------------------- |
| [Azure Key Vault Configuration Provider](https://docs.microsoft.com/en-us/aspnet/core/security/key-vault-configuration?view=aspnetcore-3.0) (*Security* topics) | Azure Key Vault                    |
| [Azure App Configuration Provider](https://docs.microsoft.com/en-us/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation) | Azure App Configuration            |
| [Command-line Configuration Provider](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.0#command-line-configuration-provider) | Command-line parameters            |
| [Custom configuration provider](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.0#custom-configuration-provider) | Custom source                      |
| [Environment Variables Configuration Provider](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.0#environment-variables-configuration-provider) | Environment variables              |
| [File Configuration Provider](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.0#file-configuration-provider) | Files (INI, JSON, XML)             |
| [Key-per-file Configuration Provider](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.0#key-per-file-configuration-provider) | Directory files                    |
| [Memory Configuration Provider](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.0#memory-configuration-provider) | In-memory collections              |
| [User secrets (Secret Manager)](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-3.0) (*Security* topics) | File in the user profile directory |



### A typical sequence of configuration providers is:

1. Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)
2. [Azure Key Vault](https://docs.microsoft.com/en-us/aspnet/core/security/key-vault-configuration?view=aspnetcore-3.0)
3. [User secrets (Secret Manager)](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-3.0) (Development environment only)
4. Environment variables
5. Command-line arguments



## Configure host builder with ConfigureHostConfiguration

```C#
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureHostConfiguration(config =>
        {
            var dict = new Dictionary<string, string>
            {
                {"MemoryCollectionKey1", "value1"},
                {"MemoryCollectionKey2", "value2"}
            };

            config.AddInMemoryCollection(dict);
        })
```



## Configure app builder with ConfigureAppConfiguration

```C#
public class Program
{
    public static Dictionary<string, string> arrayDict = 
        new Dictionary<string, string>
        {
            {"array:entries:0", "value0"},
            {"array:entries:1", "value1"},
            {"array:entries:2", "value2"},
            {"array:entries:4", "value4"},
            {"array:entries:5", "value5"}
        };

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddInMemoryCollection(arrayDict);
                config.AddJsonFile(
                    "json_array.json", optional: false, reloadOnChange: false);
                config.AddJsonFile(
                    "starship.json", optional: false, reloadOnChange: false);
                config.AddXmlFile(
                    "tvshow.xml", optional: false, reloadOnChange: false);
                config.AddEFConfiguration(
                    options => options.UseInMemoryDatabase("InMemoryDb"));
                config.AddCommandLine(args);
            });
}
```





## Command-line Configuration Provider

```C#
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```



## Environment Variables Configuration Provider

```C#
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call additional providers here as needed.
    // Call AddEnvironmentVariables last if you need to allow
    // environment variables to override values from other 
    // providers.
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```



### Connection strings

Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.

| Environment variable key | Converted configuration key | Provider configuration entry                                 |
| ------------------------ | --------------------------- | ------------------------------------------------------------ |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | Configuration entry not created.                             |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | Key: `ConnectionStrings:<KEY>_ProviderName`: Value: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | Key: `ConnectionStrings:<KEY>_ProviderName`: Value: `System.Data.SqlClient` |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | Key: `ConnectionStrings:<KEY>_ProviderName`: Value: `System.Data.SqlClient` |



## File Configuration Provider

### INI Configuration Provider

```C#
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```



### JSON Configuration Provider

```C#
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```



### XML Configuration Provider

```C#
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```



## Key-per-file Configuration Provider

```C#
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```



## Memory Configuration Provider

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

```



```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})

```



## GetValue

```csharp
NumberConfig = _config.GetValue<int>("NumberKey", 99);

```



## GetSection

```csharp
var configSection = _config.GetSection("section1");

```



## GetChildren

```csharp
var configSection = _config.GetSection("section2");
var children = configSection.GetChildren();

```



## Exists

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();

```



## Bind to a class (options pattern)

[Options pattern in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-3.0)



```csharp
public class Starship
{
    public string Name { get; set; }
    public string Registry { get; set; }
    public string Class { get; set; }
    public decimal Length { get; set; }
    public bool Commissioned { get; set; }
}

```



```json
{
  "starship": {
    "name": "USS Enterprise",
    "registry": "NCC-1701",
    "class": "Constitution",
    "length": 304.8,
    "commissioned": false
  },
  "trademark": "Paramount Pictures Corp. http://www.paramount.com"
}

```



```csharp
var starship = new Starship();
_config.GetSection("starship").Bind(starship);
Starship = starship;

```



## Bind to an object graph

```csharp
public class TvShow
{
    public Metadata Metadata { get; set; }
    public Actors Actors { get; set; }
    public string Legal { get; set; }
}

public class Metadata
{
    public string Series { get; set; }
    public string Title { get; set; }
    public DateTime AirDate { get; set; }
    public int Episodes { get; set; }
}

public class Actors
{
    public string Names { get; set; }
}

```



```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <tvshow>
    <metadata>
      <series>Dr. Who</series>
      <title>The Sun Makers</title>
      <airdate>11/26/1977</airdate>
      <episodes>4</episodes>
    </metadata>
    <actors>
      <names>Tom Baker, Louise Jameson, John Leeson</names>
    </actors>
    <legal>(c)1977 BBC https://www.bbc.co.uk/programmes/b006q2x0</legal>
  </tvshow>
</configuration>

```



```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;

```



```csharp
TvShow = _config.GetSection("tvshow").Get<TvShow>();

```



## Bind an array to a class

```csharp
public class Program
{
    public static Dictionary<string, string> arrayDict = 
        new Dictionary<string, string>
        {
            {"array:entries:0", "value0"},
            {"array:entries:1", "value1"},
            {"array:entries:2", "value2"},
            {"array:entries:4", "value4"},
            {"array:entries:5", "value5"}
        };

    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddInMemoryCollection(arrayDict);
            })
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}

```



```csharp
public class ArrayExample
{
    public string[] Entries { get; set; }
}

```



```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);

```



```csharp
ArrayExample = _config.GetSection("array").Get<ArrayExample>();

```



## Custom configuration provider

[link](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.0#custom-configuration-provider)



## Access configuration during startup

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}

```



## Access configuration in a Razor Pages page or MVC view

Razor page:

```html
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>


```



MVC:

```html
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>


```

