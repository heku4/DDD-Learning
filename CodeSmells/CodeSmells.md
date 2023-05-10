## Throttle, Debounce в чем различия и в каких случаях применяются.

They do the exact same thing (rate limiting) but while throttle is being called it fires your function periodically, while debounce just fires once at the end.
Throttle fires throughout, debounce only fires at the end.

Example: If you're scrolling, throttle will slowly call your function while you scroll (every X milliseconds). Debounce will wait until after you're done scrolling to call your function.


## Code Smells with examples

### Bloaters (Long Method, Large Class, Primitive Obsession, Long Parameter List, Data Clumps)

- #### Long Methods
It is recommended that any method which is longer than 10 lines of code is a good candidate for refactoring.
**How to fix:**
1. Extract method - split method to a several smaller methods
2. Decompose conditional - add condition and call new method in condition's block


- #### Large Classes
It’s simple because a single class should have a single responsibility, that’s the SRP rule. Each class should have one responsibility or one reason to change.


- #### Primitive Obsession
Primitive Obsession simply means we are using primitive types (int, String, …) way too much instead of objects. 
Primitive Obsession issues:

1. Major cause of long parameter lists.
2. Causes code duplication.
3. Not type safe and prone to errors.

**For example:**
we want to get VAT for a specific country and we mistyped the name France.

```csharp
  GetCountryVat("Fance");
```

**How to fix:**
1. One refactoring technique is called preserve whole object, in which we pass in the entire object as a parameter instead of its parts.


- #### Long Parameter List
Long parameter list happens when our method has 4 or more parameters. We need to have as few as possible, one or two is typically fine, three parameters or arguments should make us think if we can simplify our method somehow.

Long parameter list issues:

1. Hard to understand.
2. Difficult to remember the position of each argument.
3. When there are too many parameters, most likely, the method tries to do too many things or has too many reasons to change.

**For example:**
If we read the above code and we have no idea what the magic number and the magic boolean do, neither how the string and the order object is used. We have to go inside the method and read through the implementation to understand what the method does.

```csharp
int Calculate(10, false, "US", new Order());
```
  
**How to fix:**
- Refactoring:
- Replace Parameter with Query
- Preserve the Whole Object
- Introduce Parameter Object
- Remove Flag Argument
- Combine Methods into Class

- #### Data Clumps
Data clumps are a group of variables which are passed around together (in a clump) throughout various parts of the program. The key point is that they always go together, or they have to. In a way, data clumps are quite similar to primitive obsession.

**For example:**

```csharp
void BookRoom(DateTime startTime, DateTime endTime, int roomNumber);
```
**How to fix:**

```csharp
public class BookingDates
{
     private DateTime _start;
     private DateTime _end;

     public BookingDates(DateTime start, DateTime end) 
     {
         this.start = start;
         this.end = end;
     }
 }


void BookRoom(BookingDate bookingDates, int roomNumber);
```

### Object-Orientation Abusers (Switch Statements, Temporary Field, Refused Bequest, Alternative Classes with Different Interfaces)

a) [csharp-refactoring-object-orientation-abusers](https://code-maze.com/csharp-refactoring-object-orientation-abusers/)
b) [Introduction to Object oriented Abusers](https://ducmanhphan.github.io/2020-01-24-Fixing-object-oriented-abusers/#alternative-classes-with-different-interfaces)

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



### Change Preventers (Divergent Change, Shotgun Surgery, Parallel Inheritance Hierarchies)
- #### Divergent Change

**For example:**

```csharp
```

**How to fix:**


```csharp
```

- #### Shotgun Surgery

**For example:**

```csharp
```

**How to fix:**


```csharp
```

- #### Parallel Inheritance Hierarchies

**For example:**

```csharp
```

**How to fix:**


```csharp
```


### Dispensables (Comments, Duplicate Code, Lazy Class, Data Class, Dead Code, Speculative Generality)

### Couplers (Feature Envy, Inappropriate Intimacy, Message Chains, Middle Man)


## Техники рефакторинга кода (Refactoring Techniques) with examples

Composing Methods (Extract Method, Inline Method, Extract Variable, Inline Temp, Replace Temp with Query, Split Temporary Variable, Remove Assignments to Parameters, Replace Method with Method Object, Substitute Algorithm)

Moving Features between Objects (Move Method, Move Field, Extract Class, Inline Class, Hide Delegate, Remove Middle Man, Introduce Foreign Method, Introduce Local Extension)

Organizing Data (Change Value to Reference, Change Reference to Value, Duplicate Observed Data, Self Encapsulate Field, Replace Data Value with Object, Replace Array with Object, Change Unidirectional Association to Bidirectional, Change Bidirectional Association to Unidirectional, Encapsulate Field, Encapsulate Collection, Replace Magic Number with Symbolic Constant, Replace Type Code with Class, Replace Type Code with Subclasses, Replace Type Code with State/Strategy, Replace Subclass with Fields)

Simplifying Conditional Expressions (Consolidate Conditional Expression, Consolidate Duplicate Conditional Fragments, Decompose Conditional, Replace Conditional with Polymorphism, Remove Control Flag, Replace Nested Conditional with Guard Clauses, Introduce Null Object, Introduce Assertion)

Simplifying Method Calls (Add Parameter, Remove Parameter, Rename Method, Separate Query from Modifier, Parameterize Method, Introduce Parameter Object, Preserve Whole Object, Remove Setting Method, Replace Parameter with Explicit Methods, Replace Parameter with Method Call, Hide Method, Replace Constructor with Factory Method, Replace Error Code with Exception, Replace Exception with Test)

Dealing with Generalization (Pull Up Field, Pull Up Method, Pull Up Constructor Body, Push Down Field, Push Down Method, Extract Subclass, Extract Superclass, Extract Interface, Collapse Hierarchy, Form Template Method, Replace Inheritance with Delegation, Replace Delegation with Inheritance)


##  Design Patterns with examples

Factory
Factory Method
Abstract Factory
Builder
Singleton
Prototype
Adapter
Bridge
Composite
Decorator
Facade
Flyweight
Proxy
Chain of Responsibility
Command
Iterator
Mediator
Memento
Observer
State
Strategy
Template Method
Visitor
Interpreter
Object Pool
Delegation
Extension object
Marker interface
Servant
Specification
Fluent Interface
