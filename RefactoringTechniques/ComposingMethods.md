## Composing Methods 

- ### Extract Method
Transofrming a code fragment to a method which name explains the purpose.

**Before:**

```csharp

void PrintOwing(double amount) 
{
    PrintBanner();

    // print details
    Console.WriteLine("name: " + _name);
    Console.WriteLine("amount " + amount);
}
```

**After:**

```csharp

void PrintOwing(double amount)
{
    PrintBanner();
    PrintDetails(amount);
}

void PrintDetails (double amount) 
{
    Console.WriteLine("name:" + _name);
    Console.WriteLine("amount" + amount);
}

```


- ### Inline Method
Move method's body to a calling code and remove the method.

**Before:**

```csharp

int GetRating() 
{
    return (MoreThanFiveLateDeliveries()) ? 2 : 1;
}

bool MoreThanFiveLateDeliveries() {
 return _numberOfLateDeliveries > 5;
}

```

**After:**

```csharp

int GetRating() 
{
    return (_numberOfLateDeliveries > 5) ? 2 : 1;
}

```


- ### Extract Variable
Expressions can become very complex and hard to read.

**Before:**

```csharp

return order.Quantity * order.ItemPrice -
      Math.Max(0, order.Quantity - 500) * order.ItemPrice * 0.05 +
      Math.Min(order.Quantity * order.ItemPrice * 0.1, 100);

```

**After:**

```csharp

const basePrice = order.Quantity * order.ItemPrice;
const quantityDiscount = Math.Max(0, order.Quantity - 500) * order.ItemPrice * 0.05;
const shipping = Math.Min(basePrice * 0.1, 100);

return basePrice - quantityDiscount + shipping;

```


- ### Inline Temp
In most of cases an Inline Temp uses with Replace Temp with Query.

**Before:**

```csharp

var basePrice = order.GetBasePrice();
return (basePrice > 1000)
```

**After:**

```csharp

return (order.GetBasePrice() > 1000)

```


- ### Replace Temp with Query
Local variables often bloat methods also they make using of `Extract Method` difficult. 
Еurning variables into their own functions makes breaking up a large function easier to extract parts of the function

**Before:**

```csharp

double GetPrice() 
{
    int basePrice = _quantity * _itemPrice;
    double discountFactor;
    if (basePrice > 1000)
    {
        discountFactor = 0.95;
    }
    else
    {
        discountFactor = 0.98;
    } 

    return basePrice * discountFactor;
}

```

**After:**

```csharp

double GetPrice() 
{
    return BasePrice() * DiscountFactor();
}

private double DiscountFactor() 
{
    if (BasePrice() > 1000)
    {
        return 0.95;
    } 
    
    return 0.98;
}

double BasePrice()
{
    return _quantity * _itemPrice
}

```


- ### Split Temporary Variable
Every temp variable must be used for separated methods.

**Before:**

```csharp

double GetDistanceTravelled (int time) 
{
    var result;
    var acc = _primaryForce / _mass;
    var primaryTime = Math.min(time, _delay);
    result = 0.5 * acc * primaryTime * primaryTime;

    var secondaryTime = time - _delay;

    if (secondaryTime > 0) 
    {
        var primaryVel = acc * _delay;
        acc = (_primaryForce + _secondaryForce) / _mass;
        result += primaryVel * secondaryTime + 0.5 * acc * secondaryTime *
        secondaryTime;
    }

    return result;
}

```

**After:**

The result variable is same but acc has changed

```csharp

double GetDistanceTravelled (int time) 
{
    var result;
    var acc = _primaryForce / _mass;
    var primaryTime = Math.min(time, _delay);
    result = 0.5 * acc * primaryTime * primaryTime;

    var secondaryTime = time - _delay;

    if (secondaryTime > 0) 
    {
        var primaryVel = acc * _delay;
        var secondaryAcc = (_primaryForce + _secondaryForce) / _mass;
        result += primaryVel * secondaryTime + 0.5 * secondaryAcc * secondaryTime *
        secondaryTime;
    }

    return result;
}

```


- ### Remove Assignments to Parameters
Improves code readability and safety.

**Before:**

```csharp

int Discount (int inputVal, int quantity, int yearToDate) 
{
    if (inputVal > 50) inputVal -= 2;
    if (quantity > 100) inputVal -= 1;
    if (yearToDate > 10000) inputVal -= 4;

    return inputVal;
}

```

**After:**

```csharp

int Discount (int inputVal, int quantity, int yearToDate) 
{
    int result = inputVal;
    if (inputVal > 50) inputVal -= 2;
    if (quantity > 100) inputVal -= 1;
    if (yearToDate > 10000) inputVal -= 4;

    return result;
}

```


- ### Replace Method with Method Object
If a big banch of local variables don't give you a chance to extract method than create object with private fields instead of these local variables.

**Before:**

```csharp

public class Account
{
    private int _ratio;

    int CalculateSomething(int inputValue, int firstQuantity, int secondQuantity)
    {
        var calculatedNumber1 = (inputValue * firstQuantity) + _ratio;
        var calculatedNumber2 = (inputValue * secondQuantity) + 200;

        if(firstQuantity > secondQuantity)
        {
            calculatedNumber2 * 10;
        }

         var calculatedNumber3 = calculatedNumber2 * 2

         return calculateNumber3 - 1;
    }
}

```

**After:**

```csharp

public class Account
{
    private int _ratio;

    int CalculateSomething(int inputValue, int firstQuantity, int secondQuantity)
    {
        return new SomethingCalculator(this, inputValue, firstQuantity, secondQuantity).Compute();
    }
}

public class SomethingCalculator()
{
    private Account _account;
    private int _inputValue;
    private int _firstQuantity;
    private int _secondQuantity;
    private int _calculatedNumber1;
    private int _calculatedNumber2;
    private int _calculatedNumber3;

    public int Compute()
    {
        // Now we can extract methods from CalculateSomething

        CalculateNumber1();
        CalculateNumber2();
        return CalculateNumber3();
    }

    private int CalculateNumber1()
    {
        return _inputValue * _firstQuantity + _account.GetRatio();
    }

    private int CalculateNumber2()
    {
         _calculatedNumber2 = _inputValue * _secondQuantity + 200;

        if(_firstQuantity > _secondQuantity)
        {
            calculatedNumber2 * 10;
        }
    }

    private int CalculateNumber3()
    {
        return calculatedNumber2 * 2 - 1
    }
}

```

- ### Substitute Algorithm
If I find a clearer way to do something, I replace the complicated way with the clearer way.

**Before:**

```csharp

string FoundPerson(String[] people){
    for (int i = 0; i < people.length; i++) {
        if (people[i].equals ("Don"))
        {
        return "Don";
        }
        if (people[i].equals ("John"))
        {
        return "John";
        }
        if (people[i].equals ("Kent"))
        {
        return "Kent";
        }
    }

    return "";
}

```

**After:**

```csharp

string FoundPerson(String[] people)
{
    var candidates = new List<string>(){"Don", "John", "Kent"};
    for (int i=0; i<people.Count; i++)
    {
        if (candidates.contains(people[i]))
        {
            return people[i];
        }
    }
    
    return "";
}

```