# Handle errors
Date: 02.10.2019  
[Original](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/error-handling?view=aspnetcore-3.0)  

## Developer Exception Page
``` csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
}
else
{
    app.UseExceptionHandler("/Error");
    app.UseHsts();
}
```

``` csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

``` csharp
var exceptionHandlerPathFeature =
    HttpContext.Features.Get<IExceptionHandlerPathFeature>();
if (exceptionHandlerPathFeature?.Error is FileNotFoundException)
{
    ExceptionMessage = "File error thrown";
}
if (exceptionHandlerPathFeature?.Path == "/index")
{
    ExceptionMessage += " from home page";
}
```

## Exception handler lambda
``` csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
}
else
{
   app.UseExceptionHandler(errorApp =>
   {
        errorApp.Run(async context =>
        {
            context.Response.StatusCode = 500;
            context.Response.ContentType = "text/html";

            await context.Response.WriteAsync("<html lang=\"en\"><body>\r\n");
            await context.Response.WriteAsync("ERROR!<br><br>\r\n");

            var exceptionHandlerPathFeature = 
                context.Features.Get<IExceptionHandlerPathFeature>();

            // Use exceptionHandlerPathFeature to process the exception (for example, 
            // logging), but do NOT expose sensitive error information directly to 
            // the client.

            if (exceptionHandlerPathFeature?.Error is FileNotFoundException)
            {
                await context.Response.WriteAsync("File error thrown!<br><br>\r\n");
            }

            await context.Response.WriteAsync("<a href=\"/\">Home</a><br>\r\n");
            await context.Response.WriteAsync("</body></html>\r\n");
            await context.Response.WriteAsync(new string(' ', 512)); // IE padding
        });
    });
    app.UseHsts();
}
```

## UseStatusCodePages
``` csharp
app.UseStatusCodePages();
```

## UseStatusCodePages with format string
``` csharp
app.UseStatusCodePages(
    "text/plain", "Status code page, status code: {0}");  
```

## UseStatusCodePages with lambda
``` csharp
app.UseStatusCodePages(async context =>
{
    context.HttpContext.Response.ContentType = "text/plain";

    await context.HttpContext.Response.WriteAsync(
        "Status code page, status code: " + 
        context.HttpContext.Response.StatusCode);
});
```

## UseStatusCodePagesWithRedirect
``` csharp
app.UseStatusCodePagesWithRedirects("/StatusCode?code={0}");
```

## UseStatusCodePagesWithReExecute
``` csharp
app.UseStatusCodePagesWithReExecute("/StatusCode","?code={0}");
```

## Disable status code pages
``` csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## Database error page
``` csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```


















