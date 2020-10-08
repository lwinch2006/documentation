# Make HTTP requests using IHttpClientFactory
Date: 07.10.2019  
[Original](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/http-requests?view=aspnetcore-3.0)  

## Basic usage
``` csharp
services.AddHttpClient();
```

``` csharp
public class BasicUsageModel : PageModel
{
    private readonly IHttpClientFactory _clientFactory;

    public BasicUsageModel(IHttpClientFactory clientFactory)
    {
        _clientFactory = clientFactory;
    }
    
    public async Task OnGet()
    {
        var client = _clientFactory.CreateClient();
    }
}
```

## Named clients
``` csharp
services.AddHttpClient("github", c =>
{
    c.BaseAddress = new Uri("https://api.github.com/");
    
    // Github API versioning
    c.DefaultRequestHeaders.Add("Accept", "application/vnd.github.v3+json");
    
    // Github requires a user-agent
    c.DefaultRequestHeaders.Add("User-Agent", "HttpClientFactory-Sample");
});
```

``` csharp
public class NamedClientModel : PageModel
{
    private readonly IHttpClientFactory _clientFactory;
    
    public NamedClientModel(IHttpClientFactory clientFactory)
    {
        _clientFactory = clientFactory;
    }    
    
    public async Task OnGet()
    {
        var client = _clientFactory.CreateClient("github");
    }    
}
```

## Typed clients
```csharp
public class GitHubService
{
    public HttpClient Client { get; }

    public GitHubService(HttpClient client)
    {
        ...
        
        Client = client;
    }
    
    public async Task<IEnumerable<GitHubIssue>> GetAspNetDocsIssues()
    {
        ...
    }
```

``` csharp
services.AddHttpClient<GitHubService>();
```

``` csharp
public class TypedClientModel : PageModel
{
    private readonly GitHubService _gitHubService;
    
    public TypedClientModel(GitHubService gitHubService)
    {
        _gitHubService = gitHubService;
    }
    
    public async Task OnGet()
    {
        var latestIssues = await _gitHubService.GetAspNetDocsIssues();
    }
}
```

``` csharp
services.AddHttpClient<RepoService>(c =>
{
    c.BaseAddress = new Uri("https://api.github.com/");
    c.DefaultRequestHeaders.Add("Accept", "application/vnd.github.v3+json");
    c.DefaultRequestHeaders.Add("User-Agent", "HttpClientFactory-Sample");
});
```

## Generated clients
```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));
}
```

``` csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## Outgoing request middleware
```csharp
public class ValidateHeaderHandler : DelegatingHandler
{
    protected override async Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request,
        CancellationToken cancellationToken)
    {
        if (!request.Headers.Contains("X-API-KEY"))
        {
            return new HttpResponseMessage(HttpStatusCode.BadRequest)
            {
                Content = new StringContent(
                    "You must supply an API key header called X-API-KEY")
            };
        }

        return await base.SendAsync(request, cancellationToken);
    }
}
```

```csharp
services.AddTransient<ValidateHeaderHandler>();

services.AddHttpClient("externalservice", c =>
{
    // Assume this is an "external" service which requires an API KEY
    c.BaseAddress = new Uri("https://localhost:5000/");
})
.AddHttpMessageHandler<ValidateHeaderHandler>();
```

## Handle transient faults
``` csharp
services.AddHttpClient<UnreliableEndpointCallerService>()
    .AddTransientHttpErrorPolicy(p => 
        p.WaitAndRetryAsync(3, _ => TimeSpan.FromMilliseconds(600)));
```

## Dynamically select policies
``` csharp
var timeout = Policy.TimeoutAsync<HttpResponseMessage>(
    TimeSpan.FromSeconds(10));
var longTimeout = Policy.TimeoutAsync<HttpResponseMessage>(
    TimeSpan.FromSeconds(30));

services.AddHttpClient("conditionalpolicy")
// Run some code to select a policy based on the request
    .AddPolicyHandler(request => 
        request.Method == HttpMethod.Get ? timeout : longTimeout);
```

## Add multiple Polly handlers
```csharp
services.AddHttpClient("multiplepolicies")
    .AddTransientHttpErrorPolicy(p => p.RetryAsync(3))
    .AddTransientHttpErrorPolicy(
        p => p.CircuitBreakerAsync(5, TimeSpan.FromSeconds(30)));
```

## Add policies from the Polly registry
```csharp
var registry = services.AddPolicyRegistry();

registry.Add("regular", timeout);
registry.Add("long", longTimeout);

services.AddHttpClient("regulartimeouthandler")
    .AddPolicyHandlerFromRegistry("regular");
```

## HttpClient and lifetime management
[link](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/http-requests?view=aspnetcore-3.0#httpclient-and-lifetime-management)

## Logging
[link](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/http-requests?view=aspnetcore-3.0#logging)

## Configure the HttpMessageHandler
```csharp
services.AddHttpClient("configured-inner-handler")
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        return new HttpClientHandler()
        {
            AllowAutoRedirect = false,
            UseDefaultCredentials = true
        };
    });
```

## Use IHttpClientFactory in a console app
``` csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
class Program
{
    static async Task<int> Main(string[] args)
    {
        var builder = new HostBuilder()
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHttpClient();
                services.AddTransient<IMyService, MyService>();
            }).UseConsoleLifetime();

        var host = builder.Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var myService = services.GetRequiredService<IMyService>();
                var pageContent = await myService.GetPage();

                Console.WriteLine(pageContent.Substring(0, 500));
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();

                logger.LogError(ex, "An error occurred.");
            }
        }

        return 0;
    }

    public interface IMyService
    {
        Task<string> GetPage();
    }

    public class MyService : IMyService
    {
        private readonly IHttpClientFactory _clientFactory;

        public MyService(IHttpClientFactory clientFactory)
        {
            _clientFactory = clientFactory;
        }

        public async Task<string> GetPage()
        {
            // Content from BBC One: Dr. Who website (©BBC)
            var request = new HttpRequestMessage(HttpMethod.Get,
                "https://www.bbc.co.uk/programmes/b006q2x0");
            var client = _clientFactory.CreateClient();
            var response = await client.SendAsync(request);

            if (response.IsSuccessStatusCode)
            {
                return await response.Content.ReadAsStringAsync();
            }
            else
            {
                return $"StatusCode: {response.StatusCode}";
            }
        }
    }
}
```


































