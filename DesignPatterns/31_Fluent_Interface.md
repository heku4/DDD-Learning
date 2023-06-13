## Fluent Interface

Fluent Interface Pattern is a design guideline for OO languages that advices that exposed API, that is class public methods should, in order to increase readability.

```csharp
static class Program
{
    static void Main(string[] args)
    {
        var empl = new Employee();
        empl.SetFirstName("John").SetLastName("Smith").SetAge(30).Print();

        Console.ReadLine();
    }
}
public class Employee
{
    public string FirstName;
    public string LastName;
    public int Age = 0;
}

public static class EmployeeExtensions
{
    public static Employee SetFirstName(this Employee emp, string fName)
    {
        emp.FirstName = fName;
        return emp;
    }

    public static Employee SetLastName(this Employee emp, string lName)
    {
        emp.LastName = lName;
        return emp;
    }

    public static Employee SetAge(this Employee emp, int age)
    {
        emp.Age = age;
        return emp;
    }

    public static void Print(this Employee emp)
    {
        var tmp = String.Format("FirstName:{0}; LastName:{1}; Age:{2}", 
            emp.FirstName, emp.LastName, emp.Age);
        Console.WriteLine(tmp);
    }
}
```