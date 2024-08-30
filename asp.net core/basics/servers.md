# Web server implementations

Date: 26.09.2019

[Original](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/servers/?view=aspnetcore-3.0&tabs=windows)

.NET 3.x framework version

## Kestrel

Kestrel is the default web server included in ASP.NET Core project templates.

Use:

- by itself

  ![Kestrel communicates directly with the Internet without a reverse proxy server](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/_static/kestrel-to-internet2.png?view=aspnetcore-3.0)

- with a *reverse proxy server*

  ![Kestrel communicates indirectly with the Internet through a reverse proxy server, such as IIS, Nginx, or Apache](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/_static/kestrel-to-internet.png?view=aspnetcore-3.0)



### Nginx with Kestrel

For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see [Host ASP.NET Core on Linux with Nginx](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/linux-nginx?view=aspnetcore-3.0).

### Apache with Kestrel

For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see [Host ASP.NET Core on Linux with Apache](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/linux-apache?view=aspnetcore-3.0).



## ASP.NET Core server infrastructure

The [IApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder) available in the `Startup.Configure` method exposes the [ServerFeatures](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.serverfeatures#Microsoft_AspNetCore_Builder_IApplicationBuilder_ServerFeatures) property of type [IFeatureCollection](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.features.ifeaturecollection). 



## Custom servers

If the built-in servers don't meet the app's requirements, a custom server implementation can be created. The [Open Web Interface for .NET (OWIN) guide](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/owin?view=aspnetcore-3.0) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based [IServer](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.server.iserver) implementation.
