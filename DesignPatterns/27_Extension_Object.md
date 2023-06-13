## Extension Object

The pattern works by defining a separate extension objectthat is responsible for providing additional functionality to an existing object. The extensionobject
typically implements an interface that the existing object canu se to access the new functionality.

```csharp
public static class Program
{
    static void Main(string[] args)
    {
        var existingObject = new ExistingObject();
        var extensionObject = new ExtensionObject();

        existingObject.ExistingMethod();
        existingObject.UseNewMethod(extensionObject);
    }
}

public class ExistingObject
{
    public void ExistingMethod()
    {
        Console.WriteLine("Existing method called");
    }
}

// Define an interface for the extension object
public interface IExtension
{
    void NewMethod();
}
// Define the extension object that implements the IExtension interface
public class ExtensionObject : IExtension
{
    public void NewMethod()
    {
        Console.WriteLine("New method called");
    }
}

// Extend the existing object using the extension object
public static class ExistingObjectExtension
{
    public static void UseNewMethod(this ExistingObject existingObject, IExtension extension)
    {
        extension.NewMethod();
    }
}
```