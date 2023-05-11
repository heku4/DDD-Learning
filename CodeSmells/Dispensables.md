### Dispensables (Comments, Duplicate Code, Lazy Class, Data Class, Dead Code, Speculative Generality)

[code-smells.com/dispensables](https://code-smells.com/dispensables)
[code-smells-dispensable](https://bytelanguage.com/2018/05/19/code-smells-dispensable/)

- #### Comments
If you feel that the code requires comments to understand, then it is better to refactor  it such a way that it eliminates the need of comments.

You could start with refactoring techniques like Rename Method, Rename Variable, along Extract Variable and Extract Method. Quite often using asserts (introduce assertions) can also aid in increasing the readability of the code.

**For example:**

```csharp

/**
* The HelloWorld program implements an application that
* simply displays "Hello World!" to the standard output.
*
* @author  X XX
* @version 1.0
* @since   2025-03-20
*/
public class HelloWorld 
{
     ...
}
```

**How to fix:**

Delete unnecessary comments


- #### Duplicate Code
A Duplicate Code smell represents code in multiple places that is the same or very similar. 
**For example:**

```js
openLocation = async (locationNode) => {
    ...
}
```

**How to fix:**
Extract Method, Extract superclass and Extract class to fix this issue


- #### Lazy Class
There could exist classes in your code which has become obsolete or the functionalities are near-useless, you could employee techniques like Inline Class to remove it.

**How to fix:**
Class might be unnecessary, and it’s functionality could be made a part of another class.


- #### Data Class
A Data Class smell occurs when a class does not implement enough functionality itself to justify it being a class.

**For example:**

```csharp
public class Coordinates
{
    public int X {get;set;}
    public int Y {get;set;}
    public int Z {get;set;}

    public Coordinates(int x, int y, int z)
    {
        X = x;
        Y = y;
        Z = z;
    }
}

var point1 = new Coordinates(1, 2, 3);
var point2 = new Coordinates(6, 7, 8);

var distance = Math.Sqrt(...)

```

**How to fix:**

```csharp
public class Coordinates
{
    private int _x;
    private int _y;
    private int _z ;

    public Coordinates(int x, int y, int z)
    {
        _x = x;
        _y = y;
        _z = z;
    }

    public int GetX() 
    {
        return _x;
    }

    public int GetDistance(Coordinates endPoint)
    {
        return Math.Sqrt(...);
    }
}

var distance = point1.GetDistance(new Coordinates(6, 7, 8))
```


- #### Dead Code
A Dead Code smell occurs when code is not used, be it a variable, method, class, or parameter.


**How to fix:**
The dead code should be deleted


- #### Speculative Generality
Speculative Generality is where code is written with so much caution for possible future changes, that the code becomes unnecessarily complex and harder to read.


**How to fix:**

Good understanding of the purpose of your code will help you make the best judgements.