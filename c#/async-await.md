# Async/await
Date: 09.12.2019  

## Efficient concept
Use await keyword when results of tasks really needed.  
``` csharp
static async Task Main(string[] args)
{
    Coffee cup = PourCoffee();
    Console.WriteLine("coffee is ready");
    var eggsTask = FryEggsAsync(2);
    var baconTask = FryBaconAsync(3);
    var toastTask = MakeToastWithButterAndJamAsync(2);

    var eggs = await eggsTask;
    Console.WriteLine("eggs are ready");
    var bacon = await baconTask;
    Console.WriteLine("bacon is ready");
    var toast = await toastTask;
    Console.WriteLine("toast is ready");
    Juice oj = PourOJ();
    Console.WriteLine("oj is ready");

    Console.WriteLine("Breakfast is ready!");

    async Task<Toast> MakeToastWithButterAndJamAsync(int number)
    {
        var toast = await ToastBreadAsync(number);
        ApplyButter(toast);
        ApplyJam(toast);
        return toast;
    }
}
```

## When all
``` csharp
await Task.WhenAll(eggsTask, baconTask, toastTask);
Console.WriteLine("eggs are ready");
Console.WriteLine("bacon is ready");
Console.WriteLine("toast is ready");
Console.WriteLine("Breakfast is ready!");
```

## When any
``` csharp
var allTasks = new List<Task>{eggsTask, baconTask, toastTask};
while (allTasks.Any())
{
    Task finished = await Task.WhenAny(allTasks);
    if (finished == eggsTask)
    {
        Console.WriteLine("eggs are ready");
    }
    else if (finished == baconTask)
    {
        Console.WriteLine("bacon is ready");
    }
    else if (finished == toastTask)
    {
        Console.WriteLine("toast is ready");
    }
    allTasks.Remove(finished);
}
Juice oj = PourOJ();
Console.WriteLine("oj is ready");
Console.WriteLine("Breakfast is ready!");
```

## Task-based Asynchronous Pattern (TAP)
* If the class has a Get method an asynchronous Get operation that returns a Task can be named GetAsync.
* If the class already has a GetAsync method, use the name GetTaskAsync.
* If a method starts an asynchronous operation but does not return an awaitable type, its name should start with Begin, Start.
* Any data that would have been returned through an out or ref parameter should instead be returned as part of the TResult returned by Task<TResult>.
* Consider adding a CancellationToken parameter even if the TAP method's synchronous counterpart does not offer one
* If a TAP method internally uses a task’s constructor to instantiate the task to be returned, the TAP method must call Start on the Task object before returning it.

## Literature
1. [Asynchronous programming with async and await](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/async/)  
2. [Asynchronous programming patterns](https://docs.microsoft.com/en-us/dotnet/standard/asynchronous-programming-patterns/index)  
3. [Task-based Asynchronous Pattern (TAP)](https://docs.microsoft.com/en-us/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)  
4. [Event-based Asynchronous Pattern (EAP)](https://docs.microsoft.com/en-us/dotnet/standard/asynchronous-programming-patterns/event-based-asynchronous-pattern-eap)  
5. [Asynchronous Programming Model (APM)](https://docs.microsoft.com/en-us/dotnet/standard/asynchronous-programming-patterns/asynchronous-programming-model-apm)    
6. [What is the difference between asynchronous programming and multithreading?](https://stackoverflow.com/questions/34680985/what-is-the-difference-between-asynchronous-programming-and-multithreading/34681101#34681101)  
7. [The Ultimate Guide to Async/Await in C# and ASP.NET](https://exceptionnotfound.net/async-await-in-asp-net-csharp-ultimate-guide/)
