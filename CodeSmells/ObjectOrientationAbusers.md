
### Object-Orientation Abusers (Switch Statements, Temporary Field, Refused Bequest, Alternative Classes with Different Interfaces)

a. [csharp-refactoring-object-orientation-abusers](https://code-maze.com/csharp-refactoring-object-orientation-abusers/)
b. [Introduction to Object oriented Abusers](https://ducmanhphan.github.io/2020-01-24-Fixing-object-oriented-abusers/#alternative-classes-with-different-interfaces)

- #### Switch Statements
Large, complicated code that does different things depending on the condition met is prone to bugs. Bad readability. The same applies to complicated if statements.

**For example:**

```csharp
public class Plane
{
    public const int Cargo = 0; // consts for type declaring 
    public const int Passenger = 1;
    public const int Military = 2;

    public int Type { get; private set; }

    public Plane(int type)
    {
        Type = type;
    }

    public double GetCapacity() // general method for Plane class
    {
        switch (Type)
        {
            case Cargo:
                return GetCargoPlaneCapacity();
            case Military:
                return GetMiliratyPlaneCapacity();
            case Passenger:
                return GetPassengerPlaneCapacity();
            default:
                throw new ArgumentOutOfRangeException($"Incorrect value: {Type}");
        }
    }
}   
```
**How to fix:**

1. Create abstarct class

```csharp
public abstract class Plane
{
    public const int Cargo = 0;
    public const int Passenger = 1;
    public const int Military = 2;
    public abstract int Type { get; }
    public abstract double GetCapacity();
    public static Plane Create(int type)
    {
        return type switch
        {
            Cargo => new CargoPlane(),
            Passenger => new PassengerPlane(),
            Military => new MilitaryPlane(),
            _ => throw new ArgumentOutOfRangeException($"Incorrect value: {type}")
        };
    }
}
```

2. Create subclass that inherits the abstarct class

```csharp
public class CargoPlane : Plane
{
    public override int Type { get { return Cargo; } }
    public override double GetCapacity()
    {
        return 10000;
    }
}
```

We don’t have to modify any type of business logic in the Plain class but only add a new subclass.

- #### Temporary Field
The code smell refers to a situation in which a class has a field or a bunch of fields that are only temporarily needed.

**For example:**
Fields the _boughtBooksCount and the _discountThreshold fields that we added to the class and only used in the GetDiscountRate() method. As soon as we execute this method, the added fields will become useless.


```csharp
public class BookstoreCustomer
{
    private int _discountThreshold;
    private int _boughtBooksCount;
    public ICollection<string> BoughtBooks { get; set; }

    public double GetDiscountRate()
    {
        _boughtBooksCount = BoughtBooks.Count;
        _discountThreshold = GetDiscountThreshold();
        var discountBase = 0.05;
        return _discountThreshold * discountBase;
    }

    private int GetDiscountThreshold()
    {
        var firstThreshold = 1;
        var secondThreshold = 2;
        return _boughtBooksCount < 10 ? firstThreshold : secondThreshold;
    }
}
```
**How to fix:**

```csharp
public class BookstoreCustomer
{
    public ICollection<string> BoughtBooks { get; set; }
    
    public double GetDiscountRate()
    {
        var boughtBooksCount = BoughtBooks.Count;
        var discountThreshold = GetDiscountThreshold(boughtBooksCount);
        var discountBase = 0.05;
        return discountThreshold * discountBase;
    }

    private int GetDiscountThreshold(double boughtBooksCount)
    {
        var firstThreshold = 1;
        var secondThreshold = 2;
        return boughtBooksCount < 10 ? firstThreshold : secondThreshold;
    }
}
```

Now the BookstoreCustomer class no longer contains redundant fields, and local variables will be purged from memory when they are no longer needed.


- #### Refused Bequest
In the domain of object-oriented programming, a subclass can reject a bequest when it inherits fields and methods that it doesn’t require. Those rejected fields and methods are object-orientation abusers.

**For example:**
The Animal class contains two methods, Eat() and Sleep(). Then we have a Bird class, that inherits from Animal and has one additional method – Fly(). 


```csharp
public class Animal
{
    public void Eat()
    {
        Console.WriteLine("Eating...");
    }
    public void Sleep()
    {
        Console.WriteLine("Sleeping...");
    }
}

public class Bird : Animal
{
    public void Fly()
    {
        Console.WriteLine("Flying...");
    }
}
```
We would like to add a specific species of bird - a penguin.

```csharp
public class Penguin : Bird 
{ 
    public void Swim() 
    { 
        Console.WriteLine("Swimming..."); 
    } 
}
```

**How to fix:**
To improve the code, the Penguin class should not inherit from the Bird class.

```csharp
public class Animal
{
    public void Eat()
    {
        Console.WriteLine("Eating...");
    }
    public void Sleep()
    {
        Console.WriteLine("Sleeping...");
    }
}
public class Bird : Animal
{
    public void Fly()
    {
        Console.WriteLine("Flying...");
    }
}
public class Penguin : Animal
{
    public void Swim()
    {
        Console.WriteLine("Swimming...");
    }
}
```


- ####  Alternative Classes with Different Interfaces
This code smell arises when two classes are similar on the inside but distinct on the exterior. In other words, this implies that the code is similar or nearly the same but the method name or method signature is somewhat different.

**For example:**
According to the principle of Don’t Repeat Yourself, we would like to have only one method. If they do the same, then one may be redundant.

```csharp
public class Dog
{
    public string Name { get; set; }
    public void Bark()
    {
        Console.WriteLine($"{Name} makes a sound.");
    }
}

public class Cat
{
    public string Name { get; set; }
    public void Meow()
    {
        Console.WriteLine($"{Name} makes a sound.");
    }
}
```
**How to fix:**
We can achieve this by creating an abstract class or an interface, and letting these classes extend or implement it

```csharp
public abstract class Animal
{
    public string Name { get; set; }
    public abstract void MakeSound();
}

public class Dog : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine($"{Name} barks.");
    }
}

public class Cat : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine($"{Name} meows.");
    }
}
```