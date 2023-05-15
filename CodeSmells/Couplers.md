### Couplers (Feature Envy, Inappropriate Intimacy, Message Chains, Middle Man)

[code-smells.com/couplers/](https://code-smells.com/couplers/)

- #### Feature Envy
Feature Envy occurs when a method is overly reliant on another class.

**For example:**

```csharp
class ContactInfo
    {
        public string GetStreetName()
        {
            return "1 Medison Square";
        }

        public string GetCity()
        {
            return "NewYork";
        }

        public string GetState()
        {
            return "NY";
        }
    }
 
    class User
    {
        private ContactInfo _contactInfo;
 
        User(ContactInfo contactInfo)
        {
            _contactInfo = contactInfo;
        }

        public string GetFullAddress()
        {
            string city = _contactInfo.GetCity();
            string state = _contactInfo.GetState();
            string streetName = _contactInfo.GetStreetName();
            return streetName + ";" + city + ";" + state;
        }
    }
```

**How to fix:**
Extract method GetFullAddress to ContactInfo class.

```csharp
class ContactInfo
    {
        public string GetStreetName()
        {
            return "1 Medison Square";
        }

        public string GetCity()
        {
            return "NewYork";
        }

        public string GetState()
        {
            return "NY";
        }

        public string GetFullAddress()
        {
            string city = GetCity();
            string state = GetState();
            string streetName = GetStreetName();
            return streetName + ";" + city + ";" + state;
        }
    }
 
    public class User
    {
        private ContactInfo _contactInfo;
        User(ContactInfo contactInfo)
        {
            _contactInfo = contactInfo;
        }

        public string GetFullAddress()
        {
            _contactInfo.GetFullAddress();
        }
    }
```

- #### Inappropriate Intimacy
Inappropriate Intimacy occurs when one class uses service fields and methods of another class.

This is an indication that the classes should be merged, that some functionality belongs in a superclass of both original classes, or that the functionality belongs in an unrelated class.

**For example:**

```csharp
```

**How to fix:**

```csharp
```
**For example:**


- #### Message Chains
Message Chains occur when a class (class A), in order to get some data from class D, must access class B and use it to access class C, and use class C to access class D (Law of Demeter).

**For example:**

```csharp

public class Customer
{
    private readonly Address _address;

    public Address GetAddress()
    {
        return _address;
    }
}

public class Address
{
    private readonly Country _country

    public Country GetCountry()
    {
        return _country;
    }
}

if(customer.GetAddress().GetCountry().IsInEurope())
{
    // ...
}
```

**How to fix:**
Extract method IsInEurope to Customer class. And move method IsInEurope down the chain.

```csharp
public class Customer
{
    private readonly Address _address;

    public Address GetAddress()
    {
        return _address;
    }

    public bool IsInEurope()
    {
        _address.IsInEurope();
    }
}

public class Address
{
    private readonly Country _country

    public Country GetCountry()
    {
        return _country;
    }

    public bool IsInEurope()
    {
        _country.IsInEurope();
    }
}

if(customer.IsInEurope())
{
    // ...
}
```

- #### Middle Man
A Middle Man is a class that in responsible, principally, for delegation.

**For example:**

```csharp

public class Employee
{
    private Department WorkDepartment {get; private set;}

    public Employee Manager 
    {
        get {return WorkDepartment.Manager;}
    }

    public string ChargeCode
    {
        get {return WorkDepartment.ChargeCode;}
    }
}

public class Department
{
    public string ChargeCode {get; private set;}
    public Employee Manager {get; private set;}
}

var employee = new Employee();
var employeesManager = employee.Manager;

```

**How to fix:**

```csharp

public class Employee
{
    private Department WorkDepartment {get; private set;}

}

public class Department
{
    public string ChargeCode {get; private set;}
    public Employee Manager {get; private set;}
}

var employee = new Employee();
var employeesManager = employee.WorkDepartment.Manager;
```