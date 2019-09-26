# Options pattern

Date: 26.09.2019

[Original](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-3.0)

.NET Core 3.x framework version



## General options configuration 

```csharp
public class MyOptions
{
    public MyOptions()
    {
        // Set default value.
        Option1 = "value1_from_ctor";
    }
    
    public string Option1 { get; set; }
    public int Option2 { get; set; } = 5;
}
```



```csharp
services.Configure<MyOptions>(Configuration);
```



## Configure simple options with a delegate

```csharp
public class MyOptionsWithDelegateConfig
{
    public MyOptionsWithDelegateConfig()
    {
        // Set default value.
        Option1 = "value1_from_ctor";
    }
    
    public string Option1 { get; set; }
    public int Option2 { get; set; } = 5;
}
```



```csharp
services.Configure<MyOptionsWithDelegateConfig>(myOptions =>
{
    myOptions.Option1 = "value1_configured_by_delegate";
    myOptions.Option2 = 500;
});
```



## Suboptions configuration

```csharp
services.Configure<MySubOptions(Configuration.GetSection("subsection"));
```



```json
{
  "option1": "value1_from_json",
  "option2": -1,
  "subsection": {
    "suboption1": "subvalue1_from_json",
    "suboption2": 200
  }
}
```



```csharp
public class MySubOptions
{
    public MySubOptions()
    {
        // Set default values.
        SubOption1 = "value1_from_ctor";
        SubOption2 = 5;
    }
    
    public string SubOption1 { get; set; }
    public int SubOption2 { get; set; }
}
```



## Named options support with IConfigureNamedOptions

```csharp
services.Configure<MyOptions>("named_options_1", Configuration);

services.Configure<MyOptions>("named_options_2", myOptions =>
{
    myOptions.Option1 = "named_options_2_value1_from_action";
});
```



```csharp
private readonly MyOptions _named_options_1;
private readonly MyOptions _named_options_2;
```



## Configure all options with the ConfigureAll method

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```



## OptionsBuilder API

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```



## Options validation

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();

try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```



## Options post-configuration

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```



```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```



```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```



## Accessing options during startup

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```






