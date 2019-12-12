# ASP.NET Core Identity
Date: 12.12.2019

* Is an API that supports user interface (UI) login functionality.  
* Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.  

## Architecture
![architecture](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity-custom-storage-providers/_static/identity-architecture-diagram.png?view=aspnetcore-3.0)

## Model classes
* [IdentityUser](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.identityuser?view=aspnetcore-3.0)
* [IdentityRole](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.identityrole?view=aspnetcore-3.0)
* [IdentityUserClaim](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.identityuserclaim-1?view=aspnetcore-3.0)
* [IdentityUserToken](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.identityusertoken-1?view=aspnetcore-3.0)
* [IdentityUserLogin](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.identityuserlogin-1?view=aspnetcore-3.0)
* [IdentityRoleClaim](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.identityroleclaim-1?view=aspnetcore-3.0)
* [IdentityUserRole](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.identityuserrole-1?view=aspnetcore-3.0)

## Store interfaces (aka service layer)
* [IUserStore<TUser>](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1?view=aspnetcore-3.0)
* [IRoleStore<TRole>](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.irolestore-1?view=aspnetcore-3.0)
* [IUserClaimStore<TUser>](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1?view=aspnetcore-3.0)
* [IUserLoginStore<TUser>](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1?view=aspnetcore-3.0)
* [IUserRoleStore<TUser>](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1?view=aspnetcore-3.0)

## When customizing Identity
* Customize the user class
  ``` csharp
  public class ApplicationUser : IdentityUser<Guid>
  {
      public string CustomTag { get; set; }
  }  
  ```
  ``` csharp
  public class ApplicationUser : IdentityUser
  {
      public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
      public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
      public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
      public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
  }
  ```
* Customize the user store
  * [IUserStore<TUser>](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) (required)
  * Optional:
    * [IUserRoleStore](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)
    * [IUserClaimStore](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)
    * [IUserPasswordStore](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)
    * [IUserSecurityStampStore](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)
    * [IUserEmailStore](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)
    * [IUserPhoneNumberStore](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)
    * [IQueryableUserStore](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)
    * [IUserLoginStore](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)
    * [IUserTwoFactorStore](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)
    * [IUserLockoutStore](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)



  Implement only those interfaces that needed:
  ``` csharp
  public class UserStore : IUserStore<IdentityUser>,
                           IUserClaimStore<IdentityUser>,
                           IUserLoginStore<IdentityUser>,
                           IUserRoleStore<IdentityUser>,
                           IUserPasswordStore<IdentityUser>,
                           IUserSecurityStampStore<IdentityUser>
  {
      // interface implementations not shown
  }
  ```
* Customize the role class
  ``` csharp
  public class ApplicationRole : IdentityRole<Guid>
  {
      public string Description { get; set; }
  }
  ```
* Customize the role store
  * [IRoleStore<TRole>](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.irolestore-1)
* Reconfigure app to use a new storage provider
  ``` csharp
  public void ConfigureServices(IServiceCollection services)
  {
      // Add identity types
      services.AddIdentity<ApplicationUser, ApplicationRole>()
          .AddDefaultTokenProviders();

      // Identity Services
      services.AddTransient<IUserStore<ApplicationUser>, CustomUserStore>();
      services.AddTransient<IRoleStore<ApplicationRole>, CustomRoleStore>();
      string connectionString = Configuration.GetConnectionString("DefaultConnection");
      services.AddTransient<SqlConnection>(e => new SqlConnection(connectionString));
      services.AddTransient<DapperUsersTable>();

      // additional configuration
  }
  ```
















