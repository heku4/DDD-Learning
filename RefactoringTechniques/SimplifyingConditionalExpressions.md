## Simplifying Conditional Expressions

- ### Consolidate Conditional Expression

Есть ряд проверок условия, дающих одинаковый результат. Объедините их в одно условное выражение и выделите его
**Before**

```csharp
double IsWorkCondition() 
{
    if (_isColdStart) 
    {
        return false;
    }
    if (_hoursDisabled_ > 12) 
    {
        return false;
    }
    if (_faultsCount > 10) 
    {
        return false;
    }
    // Compute the disability amount.
    // ...
}
```

**After**
Можно использовать логическое "И"/ "ИЛИ"

```csharp
double IsWorkCondition()
{
    if (CheckDevice()) return false;
    // Compute the disability amount.
    // ...
}
```

- ### Consolidate Duplicate Conditional Fragments

Применяется, если один и тот же фрагмент кода присутствует во всех ветвях условного
выражения
**Before**

```csharp
if (IsSpecialDeal()) 
{
    total = price * 0.95;
    Send();
}
else 
{
    total = price * 0.98;
    Send();
}
```

**After**

```csharp
if (IsSpecialDeal())
{
    total = price * 0.95;
}
else
{
    total = price * 0.98;
}

Send();
```

- ### Decompose Conditional

Имеется сложная условная цепочка проверок (if-then-else). Можно сделать код более ясным, если выполнить его декомпозицию и заменить фрагменты кода вызовами методов, имена которых раскрывают назначение соответствующего участка кода.
**Before**

```csharp
if (date < SUMMER_START || date > SUMMER_END) 
{
    charge = quantity * winterRate;
}
else 
{
    charge = quantity * summerRate;
}
```

**After**

```csharp
if (isSummer(date))
{
    charge = SummerCharge(quantity);
}
else 
{
    charge = WinterCharge(quantity);
}
```

- ### Replace Conditional with Polymorphism

Есть условный оператор, поведение которого зависит от типа объекта. Необходимо переместить каждую ветвь условного оператора в перегруженный метод подкласса и сделать исходный метод абстрактным.
**Before**

```csharp
public class Bird 
{
  // ...
    public double GetSpeed() 
    {
        switch (type) 
        {
            case EUROPEAN:
                return GetBaseSpeed();
            case AFRICAN:
                return GetBaseSpeed() - GetLoadFactor() * _coefficient;
            case NORWEGIAN_BLUE:
                return GetBaseSpeed() * _coefficient;
            default:
                throw new Exception("Should be unreachable");
        }
    }
}
```

**After**

```csharp
public abstract class Bird 
{
    // ...
    public abstract double GetSpeed();
}

class European: Bird 
{
    public override double GetSpeed() 
    {
        return GetBaseSpeed();
    }
}
class African: Bird 
{
    public override double GetSpeed() 
    {
        return GetBaseSpeed() - GetLoadFactor() * _coefficient;
    }
}
class NorwegianBlue: Bird
{
    public override double GetSpeed() 
    {
        return GetBaseSpeed() * _coefficient;
    }
}

// client code
speed = bird.GetSpeed();
```

- ### Remove Control Flag

Лучше использовать вместо управляющего флага break или return.
**Before**

```csharp
void CheckSecurity(string[] people)
{
    var found = false;
    for (var i = 0; i < people.Length; i++)
    {
        if (!found)
        {
            if (people[i].Equals("Don"))
            {
                SendAlert();
                found = true;
            }

            if (people[i].Equals("John"))
            {
                SendAlert();
                found = true;
            }
        }
    }
}
```

**After**

```csharp
void CheckSecurity(string[] people)
{
    // remove flag
    for (int i = 0; i < people.Length; i++)
    {
        if (people[i].Equals("Don")) {
            SendAlert();
            // 1. change flag changing to break
            break;
        }
        if (people[i].Equals("John")) {
            SendAlert();
            break;
        }
    }
}
```

- ### Replace Nested Conditional with Guard Clauses

Методж использует вложенные условия из-за которых не ясен нормальный путь исполнения.
**Before**

```csharp
public class Payout
{
    // ...
    double GetPayAmount()
    {
        double result = 0;
        if (isDead) {
            result = DeadAmount();
        }
        else {
            if (isSeparated) {
                result = SeparatedAmount();
            }
            else {
                if (isRetired) {
                    result = RetiredAmount();
                }
                else {
                    result = NormalPayAmount();
                }
            }
        }
        return result;
    }
}
```

**After**

```csharp
public class Payout
{
    // ...
    double GetPayAmount()
    {
        if (isDead) {
            return DeadAmount();
        }
        if (isSeparated) {
            return SeparatedAmount();
        }
        if (isRetired) {
            return RetiredAmount();
        }
        return NormalPayAmount();
    }
}
```

- ### Introduce Null Object

Если есть многократные проверки совпадения значения с null, то можно замените значение null объектом null.
**Before**

```csharp
public class Company
{
  //…
  private Customer customer;
  
  public Customer Customer
  {
    get{ return customer; }
  }
}

public class Customer
{
  public string Name 
  {
    //…
  }

  public BillingPlan GetPlan() 
  {
    //…
  }
  public PaymentHistory GetHistory() 
  {
    //…
  }
}

public class PaymentHistory
{
  public int WeeksDelinquentInLastYear 
  {
    //…
  }
}

// client code
Customer customer = site.Customer;
string customerName;
if (customer == null)
{
    customerName = "N/A";
}
else
{
    customerName = customer.Name;
}

//…
BillingPlan plan;
if (customer == null)
{
    plan = BillingPlan.Basic();
}
else
{
    plan = customer.GetPlan();
}

//…
int weeksDelinquent;
if (customer == null)
{
    weeksDelinquent = 0;
}
else
{
    weeksDelinquent = customer.GetHistory().WeeksDelinquentInLastYear;
}
```

**After**

```csharp
public class Company
{
    //…
    private Customer customer;

    public Customer Customer => customer ?? Customer.NewNull();
}

public class Customer
{
    public virtual bool IsNull => false;

    public virtual string Name
    {
        //…
    }

    // 2. Create Fabric Method to produce NullCustomers
    public static Customer NewNull()
    {
        return new NullCustomer();
    }

    // 1.2 Make methods virtual to override it in NullObject
    public virtual BillingPlan GetPlan()
    {
        //…
    }

    public virtual PaymentHistory GetHistory()
    {
        //…
    }
}

// 1.1 Create NullCustomer class
public class NullCustomer : Customer
{
    public override bool IsNull => true;
    public override string Name => "N/A";

    public override BillingPlan GetPlan()
    {
        return BillingPlan.Basic();
    }

    public override PaymentHistory GetHistory()
    {
        return PaymentHistory.NewNull();
    }
}

public class PaymentHistory
{
    public virtual bool IsNull => false;

    public virtual int WeeksDelinquentInLastYear
    {
        //…
    }

    public static PaymentHistory NewNull()
    {
        return new NullPaymentHistory();
    }
}

public class NullPaymentHistory : PaymentHistory
{
    public override bool IsNull => true;

    public override int WeeksDelinquentInLastYear => 0;
}

// 3. Remove all null safety code from client code
Customer customer = site.Customer;
var customerName = customer.Name;

BillingPlan plan = customer.GetPlan();
var weeksDelinquent = customer.GetHistory().WeeksDelinquentInLastYear;
```

- ### Introduce Assertion

Часть кода может предполагать определенное состояние программы. Это "защитное программирование", если это состояние не будет достигнуто.
**Before**

```csharp
double GetExpenseLimit() 
{
  // Should have either expense limit or
  // a primary project.
  return (expenseLimit != NULL_EXPENSE) ?
    expenseLimit :
    primaryProject.GetMemberExpenseLimit();
}
```

**After**
В конкретном случае у работника может быть не установлены личный расход и не быть установлен проект.

```csharp
double GetExpenseLimit() 
{
    if (expenseLimit != NULL_EXPENSE || primaryProject != null)
    {
        throw new Exception("Incorrectly configured employee")
    }

  return (expenseLimit != NULL_EXPENSE) ?
    expenseLimit:
    primaryProject.GetMemberExpenseLimit();
}
```
