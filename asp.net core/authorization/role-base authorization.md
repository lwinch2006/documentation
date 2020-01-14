# Role-based authorization
Date: 14.01.2020

## User should belong to "Administrator" role
```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

## User should belong to "HRManager" `or` "Finance" role
```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

## User should belong to `both` "HRManager" `and` "Finance" roles
```csharp
[Authorize(Roles = "HRManager")]
[Authorize(Roles = "Finance")]
public class SalaryController : Controller
{
}
```

## Different scopes for different roles
```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```






