# Create authorization handlers
Date: 14.01.2020

## Define operation names
```csharp
public static class TenantOperationNames
{
    public const string Create = "TenantCreate";
    public const string Read = "TenantRead";
    public const string Update = "TenantUpdate";
    public const string Delete = "TenantDelete";
}
```

## Define operations
```csharp
public static class TenantOperations
{
    public static readonly OperationAuthorizationRequirement Create = new OperationAuthorizationRequirement { Name = TenantOperationNames.Create };
    public static readonly OperationAuthorizationRequirement Read = new OperationAuthorizationRequirement { Name = TenantOperationNames.Read };
    public static readonly OperationAuthorizationRequirement Update = new OperationAuthorizationRequirement { Name = TenantOperationNames.Update };
    public static readonly OperationAuthorizationRequirement Delete = new OperationAuthorizationRequirement { Name = TenantOperationNames.Delete };
}
```

## Define authorization handlers
```csharp
public class SupportForTenantAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Tenant>
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, OperationAuthorizationRequirement requirement, Tenant resource)
    {
        if (context.User == null)
        {
            return Task.CompletedTask;
        }

        if (context.User.IsInRole(UserRoleNames.Support))
        {
            return Task.CompletedTask;
        }

        if (requirement.Name != TenantOperationNames.Read)
        {
            return Task.CompletedTask;
        }

        context.Succeed(requirement);
        return Task.CompletedTask;
    }
}
```

## Define authorization by default
```csharp
  services.AddControllersWithViews(config =>
  {
      var authorizationPolicy = new AuthorizationPolicyBuilder().RequireAuthenticatedUser().Build();
      config.Filters.Add(new AuthorizeFilter(authorizationPolicy));
  }).AddRazorRuntimeCompilation();
```

## Add authorization hadnlers to services
```csharp
  services.AddSingleton<IAuthorizationHandler, AdministratorForTenantAuthorizationHandler>();
  services.AddSingleton<IAuthorizationHandler, SupportForTenantAuthorizationHandler>();
```

## Use authorization handlers in controllers
```csharp
if (!(await _authorizationService.AuthorizeAsync(_httpContext.User, new Tenant(), TenantOperations.Update)).Succeeded)
{
    return Forbid();
}
```

## Use authorization handlers in views
```csharp
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(Context.User, Model, TenantOperations.Update)).Succeeded)
{
    <a asp-controller="Tenants" asp-action="edit" asp-route-guid="@Model.Guid" class="btn btn-outline-secondary dka-btn">Edit</a>
}
```

Literature:
1. [https://docs.microsoft.com/en-us/aspnet/core/security/authorization/secure-data?view=aspnetcore-3.0](https://docs.microsoft.com/en-us/aspnet/core/security/authorization/secure-data?view=aspnetcore-3.0)












