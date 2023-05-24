## Organizing Data

- #### Change Value to Reference
Эта техника помогает решить проблему существования одинаковых экзепляров класса, которые на самом деле представляют один объект
**Before**

```csharp
public class Customer
{
  public string Name
  {
    get;
    set;
  }

    // TODO:Replace Constructor with Factory Method.
  public Customer(string name)
  {
    this.Name = name;
  }
}

public class Order
{
  // ...
    private Customer customer;

    public string CustomerName
    {
        get { return customer.Name; }
        set { customer.Name = value; }
    }

    public Order(string customerName)
    {
        this.customer = new Customer(customerName);
    }
}

// Client code, which uses Order class.
private static int NumberOfOrdersFor(List<Order> orders, string customer)
{
    int result = 0;

    if (orders != null)
    {
        foreach (Order order in orders)
        {
            if (string.Equals(order.CustomerName, customer))
            {
                result++;
            }
        }
    }

    return result;
}
```

**After**

```csharp
public class Customer
{
    // 1.
    private static Hashtable instances = new Hashtable();

    public string Name
    {
        get;
        private set;
    }

    private Customer(string name)
    {
        this.Name = name;
    }

    public static Customer GetByName(string name)
    {
        return (Customer) instances[name];
    }

    //TODO: This code should be executed at the program launch.
    //This code can be changed to a Factory Method (with private constructor)
    // 2.
    public static void LoadCustomers()
    {
        new Customer("Lemon Car Hire").Store();
        new Customer("Associated Coffee Machines").Store();
        new Customer("Bilston Gasworks").Store();
    }
    private void Store()
    {
        instances.Add(this.Name, this);
    }
}

public class Order
{
  // ...
  private Customer customer;

  public string CustomerName
  {
    get { return customer.Name; }
  }

  public Order(string customerName)
  {
    SetCustomer(customerName);
  }

  public SetCustomer(string customerName)
  {
    // 3.
    customer = Customer.GetByName(customerName);
  }
}
//…
// Client code, which uses Order class.
private static int NumberOfOrdersFor(List<Order> orders, string customer)
{
  int result = 0;

  if (orders != null)
  {
    foreach (Order order in orders)
    {
        // 4.
        if (string.Equals(order.CustomerName, customer))
        {
            result++;
        }
    }
  }

  return result;
}
```


- #### Change Reference to Value
Для обеспечения неизменяемости объекта, он не должен иметь публичных сеттеров или других метожов меняющих его состояние. Присвоение значений полям объекта может быть только в конструкторе.

**Before**

```csharp
public class Customer
    {
    private static Hashtable instances = new Hashtable();

    public string Name
    {
        get;
        private set;
    }

    public DateTime BirthDate
    {
        get;
        set;
    }

    private Customer(string Name)
    {
        this.Name = Name;
    }

    public static Customer Get(string name)
    {
        Customer result = (Customer)instances[name];

        if (result == null)
        {
            result = new Customer(name);
            instances.Add(name, result);
        }

        return result;
    }
}

    // Somewhere in client code
    Customer john = Customer.Get("John Smith");
    john.BirthDate = new DateTime(1985, 1, 1);
```

**After**

```csharp
public class Customer
{
    public string Name
    {
        get;
        private set;
    }
    public DateTime BirthDate
    {
        get;
        // 1. to private
        private set;
    }

    // 2. use DateTime in constructor
    public Customer(string Name, DateTime BirthDate)
    {
        this.Name = Name;
        this.BirthDate = BirthDate;
    }

    // 3.1 value objects must be equal if name and date are equal
    public override bool Equals(Object obj)
    {
        Customer other = obj as Customer;

        if (other == null)
        return false;

        return this.BirthDate == other.BirthDate && string.Equals(this.Name, other.Name, StringComparison.Ordinal);
    }

    //3.2 in case of the C# lanuage
    public override int GetHashCode()
    {
        int hashCode = 11;
        unchecked
        {
        if (Name != null)
            hashCode = hashCode * 22 + Name.GetHashCode();
        hashCode = hashCode * 22 + BirthDate.GetHashCode();
        }
        return hashCode;
    }
}

// Somewhere in client code
Customer john = new Customer("John Smith", new DateTime(1985, 1, 1));
```


- #### Duplicate Observed Data

**Before**

```csharp

```

**After**

```csharp

```


- #### Self Encapsulate Field

**Before**

```csharp

```

**After**

```csharp

```


- #### Replace Data Value with Object

**Before**

```csharp

```

**After**

```csharp

```


- #### Replace Array with Object

**Before**

```csharp

```

**After**

```csharp

```


- #### Change Unidirectional Association to Bidirectional

**Before**

```csharp

```

**After**

```csharp

```


- #### Change Bidirectional Association to Unidirectional

**Before**

```csharp

```

**After**

```csharp

```


- #### Encapsulate Field

**Before**

```csharp

```

**After**

```csharp

```


- #### Encapsulate Collection

**Before**

```csharp

```

**After**

```csharp

```


- #### Replace Magic Number with Symbolic Constant

**Before**

```csharp

```

**After**

```csharp

```


- #### Replace Type Code with Class

**Before**

```csharp

```

**After**

```csharp

```


- #### Replace Type Code with Subclasses

**Before**

```csharp

```

**After**

```csharp

```


- #### Replace Type Code with State/Strategy

**Before**

```csharp

```

**After**

```csharp

```


- #### Replace Subclass with Fields

**Before**

```csharp

```

**After**

```csharp

```