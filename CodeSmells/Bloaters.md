

Example: If you're scrolling, throttle will slowly call your function while you scroll (every X milliseconds). Debounce will wait until after you're done scrolling to call your function.

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