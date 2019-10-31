# Razor file compilation
Date: 31.10.2019  
[Original](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/view-compilation?view=aspnetcore-3.0)  

## Content
[TOC]  

## Runtime compilation
- Install the `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` NuGet package.
- Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`
