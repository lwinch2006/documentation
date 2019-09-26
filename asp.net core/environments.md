# Use multiple environments

Date: 26.09.2019

[Original](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/environments?view=aspnetcore-3.0)

.NET Core 3.x framework version

## Environments

ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname).

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    if (env.IsProduction() || env.IsStaging() || env.IsEnvironment("Staging_2"))
    {
        app.UseExceptionHandler("/Error");
    }

    app.UseStaticFiles();
    app.UseMvc();
}
```



The [Environment Tag Helper](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/tag-helpers/built-in/environment-tag-helper?view=aspnetcore-3.0) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:

```html
<environment include="Development">
    <div>&lt;environment include="Development"&gt;</div>
</environment>
<environment exclude="Development">
    <div>&lt;environment exclude="Development"&gt;</div>
</environment>
<environment include="Staging,Development,Staging_2">
    <div>
        &lt;environment include="Staging,Development,Staging_2"&gt;
    </div>
</environment>
```



## Environment-based Startup class and methods

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}
```



### Startup method conventions

```csharp
public class Startup
{
    public Startup(IConfiguration configuration)
    {
        Configuration = configuration;
    }

    public IConfiguration Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        StartupConfigureServices(services);
    }

    public void ConfigureStagingServices(IServiceCollection services)
    {
        StartupConfigureServices(services);
    }

    private void StartupConfigureServices(IServiceCollection services)
    {
        services.AddMvc()
            .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        if (env.IsProduction() || env.IsStaging() || env.IsEnvironment("Staging_2"))
        {
            app.UseExceptionHandler("/Error");
        }

        app.UseStaticFiles();
        app.UseMvc();
    }

    public void ConfigureStaging(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (!env.IsStaging())
        {
            throw new Exception("Not staging.");
        }

        app.UseExceptionHandler("/Error");
        app.UseStaticFiles();
        app.UseMvc();
    }
}
```














