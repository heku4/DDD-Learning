## Moving Features between Objects (Extract Class, Inline Class, Hide Delegate, Remove Middle Man, Introduce Foreign Method, Introduce Local Extension)

- ### Move Method
The "Move method" technique makes classes simplier and more organized.
**Before:**

```csharp

class Account
{
    private AccountType _type;
    private int _daysOverdrawn;

    double BankCharge()
    {
        double result = 4.5;

        if (_daysOverdrawn > 0)
        {
            result += OverdraftCharge();
        }

        return result;
    }

    double OverdraftCharge() 
    {
        // this method better to move in AccountType class
        // in a case of new type this methods will be linked more 
        // with the AccountType class
        if (IsPremium()) 
        {
            double result = 10;
            if (_daysOverdrawn > 7) 
            {
                result += (_daysOverdrawn - 7) * 0.85;
                return result;
            }
        }

        return _daysOverdrawn * 1.75;
    }
}

```

**After:**

```csharp

class Account
{
    // ...
    private AccountType _type;
    private int _daysOverdrawn;

    double BankCharge()
    {
        double result = 4.5;

        if (_daysOverdrawn > 0)
        {
            
            result += _type.OverdraftCharge(_daysOverdrawn);
        }

        return result;
    }
}

// pararmeter daysOverdrawn can be changed to Account account.GetDaysOverdrawn 

public class AccountType
{
    // ...
    double OverdraftCharge(int daysOverdrawn) 
    {
        if (_type.IsPremium()) 
        {
            double result = 10;
            if (daysOverdrawn > 7) 
            {
                result += (daysOverdrawn - 7) * 0.85;
                return result;
            }
        }

        //

        return daysOverdrawn * 1.75;
    }
}

```

- ### Move Field
In case the field is used frequently in another class or will be used frequently.

**Before:**

```csharp
class Account
{
    private AccountType _type;
    private double _interestRate;

    double InterestForAmountDays (double amount, int days)
    {
        return _interestRate * amount * days / 365;
    }
}
```

**After:**

```csharp

class Account
{
    private AccountType _type;
    

    double InterestForAmountDays (double amount, int days)
    {
        return _interestRate * amount * days / 365;
    }
}

public class AccountType
{
    // ...
    private double _interestRate;
    
    void SetInterestRate (double rate)
    {
        _interestRate = rate;
    }

    double GetInterestRate()
    {
        return _interestRate;
    }
}

```

- ### Extract Class
Sometimes a class works as two classes. Then we must separate them.

**Before:**

```csharp
class Person
{
    private string _name;
    private string _officeAreaCode;
    private string _officeNumber;

    public string getName() 
    {
        return _name;
    }
    public string getTelephoneNumber() 
    {
        return ("(" + _officeAreaCode + ") " + _officeNumber);
    }

    string getOfficeAreaCode() 
    {
        return _officeAreaCode;
    }

    void setOfficeAreaCode(string arg) 
    {
        _officeAreaCode = arg;
    }

    string getOfficeNumber() 
    {
        return _officeNumber;
    }

    void setOfficeNumber(string arg) 
    {
        _officeNumber = arg;
    }

}

```

**After:**
First of all : use "Move Field" to all fields which will be used in a new class. Then use "Move Method".

```csharp
class TelephoneNumber
{
    private string _number;
    private string _areaCode;

    public string GetTelephoneNumber() 
    {
        return ("(" + _areaCode + ") " + _number);
    }

    string GetAreaCode() 
    {
        return _areaCode;
    }

    void SetAreaCode(string arg) 
    {
        _areaCode = arg;
    }

    string GetNumber() 
    {
        return _number;
    }

    void SetNumber(string arg) 
    {
        _number = arg;
    }
}

class Person
{
    private TelephoneNumber _officeTelephone = new TelephoneNumber();
    private string _name;

    public string GetName() 
    {
        return _name;
    }

    public string GetTelephoneNumber()
    {
        return _officeTelephone.GetTelephoneNumber();
    }
}
 
```

- ### Inline Class
Sometimes a class does too little. This tecnique is opposite to the "Move Class".

**Before:**

```csharp

class TelephoneNumber
{
    private string _number;
    private string _areaCode;

    public string GetTelephoneNumber() 
    {
        return ("(" + _areaCode + ") " + _number);
    }

    string GetAreaCode() 
    {
        return _areaCode;
    }

    void SetAreaCode(string arg) 
    {
        _areaCode = arg;
    }

    string GetNumber() 
    {
        return _number;
    }

    void SetNumber(string arg) 
    {
        _number = arg;
    }
}

class Person
{
    private TelephoneNumber _officeTelephone = new TelephoneNumber();
    private string _name;

    public string GetName() 
    {
        return _name;
    }

    public string GetTelephoneNumber()
    {
        return _officeTelephone.GetTelephoneNumber();
    }
}
 
var martin = new Person();
martin.getOfficeTelephone().setAreaCode ("781");

```

**After:**

```csharp

class Person
{
    private string _name;
    private string _officeAreaCode;
    private string _officeNumber;

    public string getName() 
    {
        return _name;
    }
    public string getTelephoneNumber() 
    {
        return ("(" + _officeAreaCode + ") " + _officeNumber);
    }

    string getOfficeAreaCode() 
    {
        return _officeAreaCode;
    }

    void setOfficeAreaCode(string arg) 
    {
        _officeAreaCode = arg;
    }

    string getOfficeNumber() 
    {
        return _officeNumber;
    }

    void setOfficeNumber(string arg) 
    {
        _officeNumber = arg;
    }
}

var martin = new Person();
martin.setAreaCode ("781");

```

- ### Hide Delegate

**Before:**

```csharp

class Person 
{
    private Department _department;

    public Department GetDepartment() 
    {
        return _department;
    }

    public void SetDepartment(Department arg) 
    {
        _department = arg;
    }
}

public class Department 
{
    private string _chargeCode;
    private Person _manager;

    public Department (Person manager) 
    {
        _manager = manager;
    }

    public Person GetManager() 
    {
        return _manager;
    }
}

var person = new Person();
manager = person.GetDepartment().GetManager();

```

**After:**

```csharp

class Person 
{
    private Department _department;

    public Department GetDepartment() 
    {
        return _department;
    }

    public void SetDepartment(Department arg) 
    {
        _department = arg;
    }

    public Person GetManager() {
        return _department.getManager();
    }
}

var person = new Person();
manager = person.GetManager();

```

- ### Remove Middle Man
Class does only delegation work. Client can make request to delegate class by itself. A lot of simple delegation methods will bloat the main class.

**Before:**

```csharp

class Person
{

    private Department _department;

    public Person GetManager() 
    {
        return _department.GetManager();
    }
}

class Department
{
    private Person _manager;

    public Department(Person manager) 
    {
        _manager = manager;
    }
}

var manager = person.GetManager();

```

**After:**

```csharp

class Person
{

    private Department _department;

    public Department GetDepartment() 
    {
        return _department;
    }
}

var manager = person.GetDepartment().GetManager();

```

- ### Introduce Foreign Method
It helps when you are trying to add new Method to a Class but this Class is closed for modification. 

**Before:**

```csharp

class Report 
{
  // ...
  void SendReport() 
  {
    var nextDay = previousEnd.AddDays(1);
    // ...
  }
}
```

**After:**

```csharp

class Report
{
  // ...
    void SendReport() 
    {
        DateTime nextDay = NextDay(previousEnd);
        // ...
    }

    private static DateTime NextDay(DateTime date) 
    {
        return date.AddDays(1);
    }
}

```

- ### Introduce Local Extension
The same as `Introduce Foreign Method` but used when you need move many methods to a Class which closed for modification.

**Before:**

```csharp

public class Account
{
  // ...
  double SchedulePayment()
  {
    DateTime paymentDate = GetNearFirstDate(previousDate);

    // Issue a payment using paymentDate.
    // ...
  }

  // TODO: Foreign method. Should be in the DateTime class.
  public static DateTime GetNearFirstDate(DateTime date)
  {
    if (date.Day == 1)
      return date;

    DateTime nextDate = date.AddMonths(1);
    
    return new DateTime(nextDate.Year, nextDate.Month, 1);
  }
}

```

**After:**

```csharp

// Local extension class.
public class MfDateTimeWrap
{
  private DateTime _date;

  public MfDateTimeWrap(): this(new DateTime())
  {}
  public MfDateTimeWrap(DateTime date)
  {
    _date = date;
  }

  public DateTime GetNearFirstDate()
  {
    if (_date.Day == 1)
      return _date;

    DateTime nextDate = _date.AddMonths(1);
    
    return new DateTime(nextDate.Year, nextDate.Month, 1);
  }
}

```