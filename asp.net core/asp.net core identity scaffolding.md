# Scaffold Identity in ASP.NET Core projects

1. Install generator
``` Shell
dotnet tool install -g dotnet-aspnet-codegenerator
```  
  
2. Add NuGet package to the project
``` Shell
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```  
  
3. Run command to see all available options
``` Shell
dotnet aspnet-codegenerator identity -h
```
  
4. Run the following command to generate all files with given DbContext and SQLite as database
``` Shell
dotnet aspnet-codegenerator identity -dc Dka.AspNetCore.TestingAuthentication.Data.ApplicationDbContext -sqlite
```

## FAQ
### Why Asp.Net Core 2.1 Identity Use Razor Pages ([link](https://stackoverflow.com/questions/53301538/why-asp-net-core-2-1-identity-use-razor-pages))?
```
The only current option also seems to be using Razor Pages for the Identity UI. Some of us want full control over how we use Identity so that we can customize it to our needs. The current setup is simply unacceptable. If my entire project is using MVC, I don't want Identity living in its own folder off in la-la land as Razor Pages. It makes the project structure a mess, and there's just no reason for it.  
Maintaining two versions of the same codebase (MVC and Razor Pages) is very expensive for us and there are no real benefits from having an MVC implementation compared to a Razor Pages version. Both flavors are fully in ASP.NET. Moving the code out of the area should be relatively straight forward, as well as converting it to MVC from Razor Pages. It probably simply involves moving the pages from the area into your main app Pages folder and calling AddIdentity instead of AddDefaultIdentity.  

Please either fix the options so that those who want it can take full control of Identity and use it as they wish, whether that's the MVC approach or the Razor Pages approach. Or provide us with sufficient documentation so that we may add Identity to a blank project without using the Identity UI library or some magic scaffolding voodoo. As it stands now, there doesn't appear to be any documentation on Identity that doesn't rely on the new scaffolding system and Identity UI.  
The default UI is completely optional. You should be able to just scaffold the pages into your project and do whatever you want from there, whether that's convert them to an MVC flavor or move them out of the area into your main project.  
```



