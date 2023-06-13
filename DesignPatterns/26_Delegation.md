## Delegation
The delegation design pattern allows an object to delegate one or more tasks to a helper object. Two classes are used to achieve this; the delegate and delegator, both which realise a common interface.

- Caller: This is the consumer making a call to the requested method.
- SomeMethod: This is a concrete implementation of the method being called in the enclosing class.
- FinalMethod: This is the concrete implementation of the method being called from the SomeMethod parent; this is the delegated action.
- IDelegationInterface: This is an interface, defining the appearance of a class containing a final method as a concrete implementation, allowing the final implementation to be changed.

```csharp
class Program
{
    static void Main(string[] args)
    {
        var delegationClass = new DelegationClass();
        delegationClass.SomeMethod(1, 2, 3); // Clinet = Caller
    }
}

public interface IDelegationInterface
{
    void FinalMethod(int a, int b, int c);
}

class DelegationClass
{
    private IDelegationInterface _finalClass;

    public void SomeMethod(int a, int b, int c)
    {
        _finalClass = new FinalClass();

        _finalClass.FinalMethod(a, b, c);
    }

}

public class FinalClass: IDelegationInterface
{
    public void FinalMethod(int a, int b, int c)
    {
        Console.WriteLine("Final method called in the delegation chain.");
    }
}

public class FinalClass2: IDelegationInterface
{
    public void FinalMethod(int a, int b, int c)
    {
        Console.WriteLine("Final method 2 called in the delegation chain.");
    }
}


```