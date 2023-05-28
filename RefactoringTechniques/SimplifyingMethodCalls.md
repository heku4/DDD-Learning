## Simplifying Method Calls

- ### Add Parameter

Метод нуждается в дополнительной информации от вызывающего.

- ### Remove Parameter

Параметр более не используется в теле метода.

- ### Rename Method

Имя метода не раскрывает его назначения.

- ### Separate Query from Modifier
Есть метод, возвращающий значение, но, кроме того, изменяющий
состояние объекта.
Необходимо создать два метода – один для запроса и один для модификации
**Before**

```csharp

```

**After**

```csharp

```

- ### Parameterize Method

Иногда встречаются два метода, выполняющие сходные действия, но отличающиеся несколькими значениями. В этом случае можно упростить положение, заменив разные методы одним, который обрабатывает разные ситуации с помощью параметров.
**Before**

```csharp
public class Employee 
{
    private int _salary = 1000;

    public void TenPercentRaise () 
    {
        _salary *= 1.1;
    }

    public void FivePercentRaise () 
    {
        _salary *= 1.05;
    }
}
```

**After**

```csharp
class Employee 
{
    private int _salary = 1000;

    void IncreaseSalary (int coefficient) 
    {
        _salary *= coefficient;
    }
}
```

- ### Introduce Parameter Object

Часто встречается некоторая группа параметров, обычно передаваемых вместе. Эта группа может использоваться несколькими методами
одного или более классов. Такая группа классов представляет собой группу данных (data clump) и может быть заменена объектом, хранящим все эти данные.
**Before**

```csharp
public class Account
{
    private List<Transaction> transactions = new();

    public double GetFlowBetween(DateTime start, DateTime end)
    {
        double result = 0;

        foreach (var t in transactions)
            if (t.ChargeDate >= start && t.ChargeDate <= end)
                result += t.Value;

        return result;
    }
}


// client code
var flow = account.GetFlowBetween(startDate, endDate);
```

**After**

```csharp
public class Account
{
    private List<Transaction> transactions = new List<Transaction>();

    // Use new type for existing methods
    public double GetFlowBetween(DateRange range)
    {
        double result = 0;

        foreach (var t in transactions)
        {
            if (range.Includes(t.ChargeDate))
                result += t.Value;
        }

        return result;
    }
}

// 1. create DateRange class
public class DateRange
{
    public DateTime Start { get; private set; }
    public DateTime End { get; private set; }

    public DateRange(DateTime start, DateTime end)
    {
        Start = start;
        End = end;
    }

    // 2. Create usefull method
    public bool Includes(DateTime arg)
    {
        return arg >= Start && arg <= End;
    }
}

// client code
var flow = account.GetFlowBetween(new DateRange(startDate, endDate));
```

- ### Preserve Whole Object

Получение объекта с несколькими параметрами и передача всего объекта как агрумент функции, вместо его параметров.Если вызываемому объекту в дальнейшем потребуются новые данные, придется найти и изменить все вызовы этого метода
**Before**

```csharp
public class Room
{
    // ...
    public bool InTemperaturePlan(HeatingPlan plan)
    {
        int low = GetLowestTemp();
        int high = GetHighestTemp();
        return plan.WithinRange(low, high);
    }
}

public class HeatingPlan
{
    private TempRange range;

    public bool InTemperatureRange(int low, int high)
    {
        return low >= range.Low && high <= range.High;
    }
}
```

**After**

```csharp
public class Room
{
    // ...
    public bool InTemperaturePlan(HeatingPlan plan)
    {
        return plan.WithinRange(this);
    }
}

public class HeatingPlan
{
    private TempRange range;

    public bool InTemperatureRange(Room room)
    {
        return room.GetLowestTemp() >= range.Low && room.GetHighestTemp() <= range.High;
    }
}
```

- ### Remove Setting Method

**Before**
Если поле должно быть установлено в момент создания и больше никогда не изменяться, необходимо удалить все методы изменяющие его.
```csharp
public class Account
{
    // ...
    private string id;

    public string Id
    {
        get{ return id; }
        set{ id = value; }
    }

    public Account(string id)
    {
        Id = id;
    }
}
```

**After**
В простейшем случае можно сделать сеттер приватным или переместить инициализацию поля в конструктор. Случай для дочерних классов, когда сеттер родителя выполняет какую-то логику: ``private set{ id = "ID" + value; }``, рассмотрен ниже:

```csharp
public class Account
{
  // ...
    private string _id;

    public string Id
    {
        get{ return _id; }
        private set{ _id = value; }
    }

    public Account(string id)
    {
        InitializeId(id);
    }
    
    // 1. Create new method for porperty initialization and make it protected
    protected void InitializeId(string id)
    {
        Id = "ID" + id;
    }
}

public class InterestAccount: Account
{
    private double _interestRate;

    public InterestAccount(string id, double interestRate)
    {
        InitializeId(id);
        _interestRate = interestRate;
    }
}
```

- ### Replace Parameter with Explicit Methods

Метод, выполняющий разный код в зависимости от значения параметра перечислимого типа, можно заменить на отдельные методы.
**Before**

```csharp
void SetValue(string name, int value) 
{
    if (name.Equals("height")) 
    {
        height = value;
        return;
    }
    if (name.Equals("width")) 
    {
        width = value;
        return;
    }

    throw new Exception($"Invalid parameter: {name}")
}
```

**After**

```csharp
void SetHeight(int arg) 
{
    height = arg;
}
void SetWidth(int arg) 
{
    width = arg;
}
```

- ### Replace Parameter with Method Call

Если вызываемый метод может получить параметр самостоятельно, через метод, он может сам сделать это.
**Before**

```csharp
public class Order
{
    public double GetPrice()
    {
        int basePrice = Quantity * ItemPrice;
        int discountLevel;

        if (Quantity > 100)
        {
            discountLevel = 2;
        }
        else
        {
            discountLevel = 1;
        }

        var finalPrice = DiscountedPrice(basePrice, discountLevel);
        return finalPrice;
    }

    private double DiscountedPrice(int basePrice, int discountLevel)
    {
        if (discountLevel == 2)
        {
           return basePrice * 0.1;
        }
        else
        {
            return basePrice * 0.05;
        }
    }
}
```

**After**

```csharp
public class Order
{
    // 3. Use these methods in main method
    public double GetPrice()
    {
        return DiscountedPrice();
    }

    private double DiscountedPrice()
    {
        if (GetDiscountLevel() == 2)
        {
            return GetBasePrice() * 0.1;
        }
        else
        {
            return GetBasePrice() * 0.05;
        }
    }

    // 2. Create new method to get base value
    private int GetDiscountLevel()
    {
        if (Quantity > 100)
        {
            return 2;
        }
        else
        {
            return 1;
        }
    }

    // 1. Create new method to get base value
    private double GetBasePrice()
    {
        return Quantity * ItemPrice;
    }
}
```

- ### Hide Method

**Before**

```csharp

```

**After**

```csharp

```

- ### Replace Constructor with Factory Method

**Before**

```csharp

```

**After**

```csharp

```

- ### Replace Error Code with Exception

**Before**

```csharp

```

**After**

```csharp

```

- ### Replace Exception with Test

**Before**

```csharp

```

**After**

```csharp

```
