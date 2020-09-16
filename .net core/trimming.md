# Trimming
Date: 14.09.2020

## What is trimming
To resolve the size problem, we introduced an option to trim unused assemblies as part of publishing self-contained applications.
| Assembly        | Trimmed (k)           | Member Trimmed (k)  |
| ------------- |-------------:| -----:|
| System.Collections.Concurrent.dll | 184.9 | 23.5 |
| System.Collections.dll | 85.5 |   15.5 |
| System.Collections.Immutable.dll | 171.5 | 20.0 |
| System.Console.dll | 61.5 | 31.5 |
| System.Diagnostics.StackTrace.dll | 33.9 | 8.5 |
| System.IO.Compression.dll | 88.5 | 33.0 |
| ... | ... | ... |
| Total | 10,545.1 | 2,213.0 |

## Assembly-level trimming
`<TrimMode>CopyUsed</TrimMode>` in the project file

## Member-Level Trimming
`TrimMode=Link` in the project file

## Dynamic code pattern analysis
A set of attributes have been added that enables code to be annotated to tell the trimmer what code should be included, or which API usage should prevent trimming.
* DynamicallyAccessedMembers - Applied to instances of System.Type (or strings containing a type name) to tell the trimmer which members of the type will be accessed dynamically.
* UnconditionalSuppressMessage - Used to suppress a warning message from the trimmer if the use case is known to be safe.
* RequiresUnreferencedCode - Tells the trimmer that the method is incompatible with trimming and so warnings should be presented where the method is called.
* DynamicDependency - Specifies an explicit dependency from a method to other code, if the method is preserved the other code will also be preserved.

## Testing Trimmed Apps
When an app is trimmed, it is essential to perform exhaustive end-to-end testing of the published version of the application.

## Trimming and Ready2Run
Using `<TrimMode>Link</TrimMode>` will remove R2R data, unless its specified to be added by using the `<PublishReadyToRun>True</PublishReadyToRun>` option as part of dotnet publish.

## Making .NET Trimmable
...

## Removing Framework Features
...

## Blazor Web Assemblies
...

## Using attributes to provide hints to the trimmer
### Annotating Reflection

```csharp
  public IEnumerable foo(
      [DynamicallyAccessedMembers(DynamicallyAccessedMemberTypes.All)] Type t)
  {
      return Activator.CreateInstance(t) as IEnumerable;
  }

  public IEnumerable foo(
      [DynamicallyAccessedMembers(DynamicallyAccessedMemberTypes.All)] string typename)
  {
      return Activator.CreateInstance("mscorlib", typename).Unwrap() as IEnumerable;
  }
```

### Specifying dependencies
```csharp
  [DynamicDependency(DynamicallyAccessedMemberTypes.All, "System.Collections.Stack", "mscorlib")]
  [DynamicDependency(DynamicallyAccessedMemberTypes.All, "System.Collections.Queue", "mscorlib")]
  public IEnumerable foo(bool lifo)
  {
      string typename = (lifo) ? "System.Collections.Stack" : "System.Collections.Queue";
      return (IEnumerable) Activator.CreateInstance("mscorlib", typename).Unwrap();
  }
```

### Suppressing Warnings
``` csharp
  [UnconditionalSuppressMessage("ReflectionAnalysis", "IL2085:UnrecognizedReflectionPattern",
              Justification = "Literal fields on enums can never be trimmed")]
  // This will return enumValues and enumNames sorted by the values.
  private void GetEnumData(out string[] enumNames, out Array enumValues) { … }
```

### Forcing Warnings
[RequiresUnreferencedCode](https://github.com/dotnet/runtime/blob/6072e4d3a7a2a1493f514cdf4be75a3d56580e84/src/libraries/System.Private.CoreLib/src/System/Diagnostics/CodeAnalysis/RequiresUnreferencedCodeAttribute.cs#L15)

## Using XML files to provide explicit directions to the trimmer
### 3 scenarios
* Preservation
* External Attribution
* Feature switches

```xml
  <linker>
    <assembly fullname="Assembly">
      <type fullname="Namespace.A">
        <field name="MyNumericField" />
        <property name="Property1" />
        <method name="Method3" />
        <event name="Event2" />
      </type>
    </assembly>
  </linker>
```

### Preservation
The trimmer will automatically include all code that it thinks can be reached by the application.  

```xml
  <ItemGroup>
    <TrimmerRootDescriptor Include="TrimmerRoots.xml" />
  </ItemGroup>
```

```xml
  <linker>
    <assembly fullname="Assembly">
  </linker>
  <linker>
    <assembly fullname="AssemblyA" preserve="all" />
  </linker>
  <linker>
    <assembly fullname="AssemblyA, Version=0.0.0.0, Culture=neutral, PublicKeyToken=null">
  </linker>
```

```
  <linker>
    <assembly fullname="AssemblyA">
      <type fullname="AssemblyA.One" preserve="all" />
      <type fullname="AssemblyA.Two" /> 
```

### Applying Attributes via XML
```xml
  <linker>
    <assembly fullname="AssemblyA">
      <type fullname="AssemblyA.One" preserve="all" >
        <method signature="AssemblyA.IFoo LoadAddon(System.String)">
          <attribute fullname="System.Diagnostics.CodeAnalysis.RequiresUnreferencedCodeAttribute" assembly="System.Runtime">
            <argument>LoadAddon is not trimmable as the assembly and type for the addon are unknown during trimming</argument>
```

```xml
  <ItemGroup>
  	<EmbeddedResource Include="ILLink.Subsitutions.xml"/>
  <ItemGroup>
```

## Reducing the application size
Beyond member level trimming, there are two other factors that will impact the published size of the app:
* Ready To Run
* Feature Switches

### Ready To Run
As the application is run, each method will need to be JITed the first time it is invoked. Subsequent calls within the process lifetime will faster as the method will already be JITted.

### Framework feature switches
```csharp
  public static bool EnableUnsafeUTF7Encoding
  {
    get => AppContext.TryGetSwitch("System.Text.Encoding.EnableUnsafeUTF7Encoding", ref s_enableUnsafeUTF7Encoding);
  }
```

```xml
  <RuntimeHostConfigurationOption Include="System.Text.Encoding.EnableUnsafeUTF7Encoding"
    Condition="'$(EnableUnsafeUTF7Encoding)' != ''"
    Value="$(EnableUnsafeUTF7Encoding)"
    Trim="true" /> 
```

```xml
  <linker>
    <assembly fullname="System.Private.CoreLib">
      <type fullname="System.LocalAppContextSwitches">
        <method signature="System.Boolean get_EnableUnsafeUTF7Encoding()" body="stub" value="false" feature="System.Text.Encoding.EnableUnsafeUTF7Encoding" featurevalue="false" />
      </type>
    </assembly>
  </linker>
```

## Literature
* [App Trimming in .NET 5](https://devblogs.microsoft.com/dotnet/app-trimming-in-net-5/)
* [Customizing Trimming in .NET 5](https://devblogs.microsoft.com/dotnet/customizing-trimming-in-net-core-5/)
