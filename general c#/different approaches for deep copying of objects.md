
# Different approaches for deep copying of objects

Date: 24.09.2019



## Via reflection

Source: [link](https://stackoverflow.com/questions/13198658/deep-copy-using-reflection-in-an-extension-method-for-silverlight)

Namespaces

```C#
using System.Reflection;
using System.Collections.Generic;
```



Method

```C#
private readonly static object _lock = new object();

public static T cloneObject<T>(T original, List<string> propertyExcludeList)
{
	try
	{
		Monitor.Enter(_lock);
		T copy = Activator.CreateInstance<T>();
		PropertyInfo[] piList = typeof(T).GetProperties(BindingFlags.Public | BindingFlags.NonPublic | BindingFlags.Instance);

		foreach (PropertyInfo pi in piList)
		{
			if (!propertyExcludeList.Contains(pi.Name))
			{
				if (pi.GetValue(copy, null) != pi.GetValue(original, null))
				{
				    pi.SetValue(copy, pi.GetValue(original, null), null);
				}
			}
		}

		return copy;
	}
	finally
	{
		Monitor.Exit(_lock);
	}
}
```



In case of need to use construction with parameters use this

```C#
T copy = (T)Activator.CreateInstance(typeof(T), initializationParameters);
```



## Via serialization

Source: [link](https://stackoverflow.com/questions/129389/how-do-you-do-a-deep-copy-of-an-object-in-net-c-specifically)

```C#
public static T DeepClone<T>(T obj)
{
    using (var ms = new MemoryStream())
    {
        var formatter = new BinaryFormatter();
        formatter.Serialize(ms, obj);
        ms.Position = 0;

        return (T) formatter.Deserialize(ms);
    }
}
```

Remarks:

- Your class must be marked as `[Serializable]` in order for this to work.  

- Your source file must include the following code:

  ```C#
  using System.Runtime.Serialization.Formatters.Binary;
  using System.IO;
  ```

  
