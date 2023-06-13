## Marker Interface

The interface serves as metadata for types that “implement” it.

```csharp
class Program
{
    static void Main(string[] args)
    {
        ICustomer customer = new Customer("Johnson", "09876543");
        ICustomer regularCustomer = new RegularCustomer("Smith", "123243567");

        SaveCustomer(customer);
        SaveCustomer(regularCustomer);
    }
    
    static void SaveCustomer(ICustomer customer)
    {
        if (customer is IContainSensitiveInformation) // check Interface Markers
            new SecureService().SaveCustomer(customer);
        else
            new RegularService().SaveCustomer(customer);
    }
}

public interface ISaveService
{
    public void SaveCustomer(ICustomer customer);
}

public class SecureService: ISaveService
{
    public void SaveCustomer(ICustomer customer)
    {
        Console.WriteLine($"Safety first:{customer.LastName}");
    }
}

public class RegularService: ISaveService
{
    public void SaveCustomer(ICustomer customer)
    {
        Console.WriteLine($"{customer.LastName}, {customer.CreditCardNumber}");
    }
}

public interface IContainSensitiveInformation // Marker
{
 
}

public interface ICustomer
{
    public string LastName { get; set; }
    public string CreditCardNumber { get; set; }
}

public class Customer : IContainSensitiveInformation, ICustomer
{
    public string SocialSecurityName { get; set; }
    public string LastName { get; set; }
    public string CreditCardNumber { get; set; }

    public Customer(string lastName, string cardNumber)
    {
        LastName = lastName;
        CreditCardNumber = cardNumber;
    }
}

public class RegularCustomer: ICustomer
{
    public string LastName { get; set; }
    public string CreditCardNumber { get; set; }

    public RegularCustomer(string lastName, string cardNumber)
    {
        LastName = lastName;
        CreditCardNumber = cardNumber;
    }
}
```