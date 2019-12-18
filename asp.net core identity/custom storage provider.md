# Custom storage provider
Date: 18.12.2019

## Data
* Users - registered users
* User Claims - a set of statements (or claims) about the user that represent the user's identity.
* User Logins - information about the external authentication provider
* Roles - authorization groups

## Steps
### Customize models
#### User model
* Minimum properties Id and Username should be implemented

![diagram](https://docs.microsoft.com/en-us/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity/_static/image2.png)
``` csharp
public class ApplicationUser : IdentityUser<int>
{
    ...
}
```

#### Role model
* Minimum properties Id and Name should be implemented

![diagram](https://docs.microsoft.com/en-us/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity/_static/image5.png)
``` csharp
public class ApplicationRole : IdentityRole<int>
{
    ...
}
```

### Customize stores
#### User store
* Interfaces  
![diagram 1](https://docs.microsoft.com/en-us/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity/_static/image3.png)
* Interfaces in details ([link](https://docs.microsoft.com/en-us/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity#interfaces-to-implement-when-customizing-user-store))  
![diagram 2](https://docs.microsoft.com/en-us/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity/_static/image4.png)
``` csharp
public class UserStore : IUserStore<ApplicationUser>
{
    ...
}
```

#### Role store
![diagram](https://docs.microsoft.com/en-us/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity/_static/image6.png)
``` csharp
public class ApplicationRoleStore : IRoleStore<ApplicationRole>
{
    ...
}
```

### Customize Startup class
``` csharp
services
    .AddIdentityCore<ApplicationUser>()
    .AddUserStore<ApplicationUserStore>()
    .AddRoleStore<ApplicationRoleStore>()
```

## Literature
1. [Custom storage providers for ASP.NET Core Identity](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity-custom-storage-providers?view=aspnetcore-3.0)
2. [Overview of Custom Storage Providers for ASP.NET Identity](https://docs.microsoft.com/en-us/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
3. [Implementing a Custom MySQL ASP.NET Identity Storage Provider](https://docs.microsoft.com/en-us/aspnet/identity/overview/extensibility/implementing-a-custom-mysql-aspnet-identity-storage-provider)


