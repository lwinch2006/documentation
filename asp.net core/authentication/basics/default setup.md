# Default setup
Date: 18.12.2019

## Startup class
### ConfigureServices()
``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDefaultIdentity<IdentityUser>() OR
    services.AddIdentity<IdentityUser, IdentityRole>() OR
    services.AddIdentityCore<IdentityUser>()
        // Another usefull functions to call further
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI()
        .AddDefaultTokenProviders()

    services.Configure<IdentityOptions>(options =>
    {
        ...
    });

    services.ConfigureApplicationCookie(options =>
    {
        ...
    });

    services.AddAuthentication(options => 
    {
        ...
    })
    // Another usefull functions to call further
    .AddIdentityCookies()
    .AddCookie()
}
```

### Configure()
``` csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    app.UseAuthentication();
    app.UseAuthorization();
            OR
    app.UseIdentity();
}
```

## Literature
1. [Configure Identity services](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity?view=aspnetcore-3.0&tabs=visual-studio#configure-identity-services)







