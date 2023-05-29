## Dealing with Generalization

- ### Pull Up Field
Если в двух классах есть одинаковое поле, то их стоит переместить в родительский класс.

- ### Pull Up Method

Если в двух классах есть методы с идентичными результатами, то их стоит переместить в родительский класс.

- ### Pull Up Constructor Body

Если имеются конструкторы подклассов с почти идентичными телами, то стоит вызывать родительский конструктор из подклассов.
**Before:**

```csharp
public class Manager: Employee 
{
    public Manager(string name, string id, int grade) 
    {
        this.name = name;
        this.id = id;
        this.grade = grade;
    }
    // ...
}
```

**After:**

```csharp
public class Manager: Employee 
{
    public Manager(string name, string id, int grade): base(name, id)
    {
        this.grade = grade;
    }
    // ...
}
```

- ### Push Down Field

Если в родительском классе есть поле, относящееся только к некоторым из подклассов.

- ### Push Down Method

Если в родительском классе есть поведение, относящееся только к некоторым из подклассов.

- ### Extract Subclass

В классе есть функции, используемые только в некоторых случаях. Для таких функций следует выделить подкласс.
**Before:**

```csharp
public class JobItem
{
    private int _unitPrice;

    public int Quantity    { get; private set; }
    public bool IsLabor    { get; private set; }
    public Employee Employee   { get; private set; }

    public JobItem(int quantity, int unitPrice, bool isLabor, Employee employee)
    {
        Quantity = quantity;
        _unitPrice = unitPrice;
        IsLabor = isLabor;
        Employee = employee;
    }

    public int GetTotalPrice()
    {
        return Quantity * GetUnitPrice();
    }
    public int GetUnitPrice()
    {
        return IsLabor ? Employee.Rate : _unitPrice;
    }
}

public class Employee
{
    public int Rate
    { get; private set; }

    public Employee(int rate)
    {
        Rate = rate;
    }
}

// client code
var kent = new Employee(50);
var j1 = new JobItem(5, 0, true, kent);
var j2 = new JobItem(15, 10, false, null);
var total = j1.GetTotalPrice() + j2.GetTotalPrice();
```

Основной класс вычисляет цену разных работ, удобнее выделить эти работы в отдельные классы
**After:**

```csharp
public abstract class JobItem
{
    public int Quantity
    { get; private set; }

    protected JobItem(int quantity)
    {
        this.Quantity = quantity;
    }

    public int GetTotalPrice()
    {
        return Quantity * GetUnitPrice();
    }
    public abstract int GetUnitPrice();
}

// 1.1. Create Subclass
public class PartsItem: JobItem
{
    // 2.1 Pull-down field
    private int _unitPrice;

    // 1.2 Push-upp constructor
    public PartsItem(int quantity, int unitPrice): base(quantity)
    {
        this._unitPrice = unitPrice;
    }

    // 2.2 Pull-down method
    public override int GetUnitPrice()
    {
        return _unitPrice;
    }
}

// 3.1. Create Subclass
public class LaborItem: JobItem
{
    // 4.1 Pull-down field
    public Employee Employee
    { get; private set; }

    // 3.2 Push-up constructor
    public LaborItem(int quantity, Employee employee): base(quantity)
    {
        Employee = employee;
    }

    // 4.2 Pull-down method
    public override int GetUnitPrice()
    {
        return Employee.Rate;
    }
}

public class Employee
{
    public int Rate
    { get; private set; }

    public Employee(int rate)
    {
        Rate = rate;
    }
}

// Somewhere in client code
var kent = new Employee(50);
var j1 = new LaborItem(5, kent);
var j2 = new PartsItem(15, 10);
var total = j1.GetTotalPrice() + j2.GetTotalPrice();
```

- ### Extract Superclass

Для двух классов со сходынми функциями лучше сделать общий родительский класс и переместить в него общие функции.
**Before:**

```csharp
public class Employee
{
    private int _annualCost;

    public string Name { get; private set; }
    public string Id { get; private set; }

    public Employee(string name, string id, int annualCost)
    {
        Name = name;
        Id = id;
        _annualCost = annualCost;
    }

    public int GetAnnualCost()
    {
        return _annualCost;
    }
}

public class Department
{
    private List<Employee> _staff = new();

    public string Name { get; private set; }
    public int HeadCount => _staff.Count;

    public IList<Employee> Staff => _staff.AsReadOnly();

    public Department(string name)
    {
        Name = name;
    }

    public void AddStaff(Employee item)
    {
        _staff.Add(item);
    }
    
    public int GetTotalAnnualCost()
    {
        return _staff.Sum(i => i.GetAnnualCost());
    }
}
```

**After:**

```csharp

// 1. Create abstract parent class
public abstract class Company
{
    // 2.1 Push-up common fields
    public string Name { get; protected set; }
    public abstract int HeadCount { get; }

    protected Company(string name)
    {
        Name = name;
    }
 
    // 2.2 Push-up common meethods
    public abstract int GetAnnualCost();
}

public class Employee: Company
{
    private int _annualCost;

    public string Id { get; private set; }
    public override int HeadCount => 1;

    // 3.1 Pull-up constructor body 
    public Employee(string name, string id, int annualCost): base(name)
    {
        Id = id;
        _annualCost = annualCost;
    }

    public override int GetAnnualCost()
    {
        return _annualCost;
    }
}

public class Department: Company
{
    private List<Company> _items = new ();

    public override int HeadCount
    {
        get{ return _items.Sum(i => i.HeadCount); }
    }

    public IList<Company> Items => _items.AsReadOnly();

    // 3.1 Pull-up constructor body 
    public Department(string name): base(name)
    {
    }

    public void AddItem(Company item)
    {
        _items.Add(item);
    }
    public override int GetAnnualCost()
    {
        return _items.Sum(i => i.GetAnnualCost());
    }
}
```

- ### Extract Interface

Несколько клиентов пользуются одним и тем же подмножеством интерфейса класса или в двух классах часть интерфейса является общей. Несколько клиентов используют только определенное подмножество предоставляемых классом функций. Или класс должен работать с любым классом,который может обрабатывать определенные запросы. Полезно заставить подмножество
обязанностей выступать от своего собственного имени, чтобы сделать отчетливым его использование в системе. Благодаря этому легче видеть, как разделяются обязанности в системе.

Для C# такое возможно только с помощью выделения интерфейсов, так как в этом языке одиночное наследование.
**Before:**

```csharp
public class TimeSheet
{
    // ...
    public double Charge(Employee employee, int days)
    {
        double baseAmount = employee.Rate * days;

        return employee.HasSpecialSkill() ? baseAmount * 1.05 : baseAmount;
    }
}

public class Employee
{
    // ...
    public int Rate 
    { get; private set; }
    // ...
    public bool HasSpecialSkill()
    {
        // ...
    }
}
```

**After:**

```csharp
public class TimeSheet
{
    // 2. Change type Employee to Interface IBillable
    public double Charge(IBillable employee, int days)
    {
        double baseAmount = employee.Rate * days;

        return employee.HasSpecialSkill() ? baseAmount * 1.05 : baseAmount;
    }
}

// 1. Create an Interface for a subset of billing charactersitics
public interface IBillable
{
    int Rate { get; }
    bool HasSpecialSkill();
}

public class Employee: IBillable
{
    // ...
    public int Rate 
    { get; private set; }
    // ...
    public bool HasSpecialSkill()
    {
        // ...
    }
}
```

- ### Collapse Hierarchy

Если родительский класс и подкласс не различаются - их стоит объединить в один класс.

- ### Form Template Method

Есть два метода в подклассах, выполняющие аналогичные шаги в одинаковом порядке, однако эти шаги различны. Нужно сделать из этих шагов методы с одинаковой сигнатурой, чтобы
исходные методы стали одинаковыми. После этого можно их поднять в родительский класс.

Два метода выполняют во многом аналогичные шаги в одинаковой последовательности, но эти шаги разные. В таком случае можно переместить последовательность шагов в
родительский класс и позволить полиморфизму выполнить свою роль в обеспечении того, чтобы различные шаги выполняли свои действия по-разному.
**Before:**

```csharp
public class Article
{
    // ...
    public string Title { get; set; }
    public string Intro { get; set; }
    public string Body { get; set; }

    public string MarkdownView()
    {
        var output = new StringBuilder();
        output.Append("# ").Append(Title).AppendLine().AppendLine();
        output.Append("> ").Append(Intro).AppendLine().AppendLine();
        output.Append(Body).AppendLine().AppendLine();
        return output.ToString();
    }
    public string HtmlView()
    {
        var output = new StringBuilder();
        output.Append("<h2>").Append(Title).Append("</h2>").AppendLine();
        output.Append("<blockquote>").Append(Intro).Append("</blockquote>").AppendLine();
        output.Append("<p>").Append(Body).Append("</p>").AppendLine();
        return output.ToString();
    }
}
```

**After:**

```csharp
public class Article
{
    // ...
    public string Title { get; set; }
    public string Intro { get; set; }
    public string Body { get; set; }
    
    public string MarkdownView()
    {
        return new ArticleMarkdown(this).View();
    }
    public string HtmlView()
    {
        return new ArticleHtml(this).View();
    }
}

// 1.1 Use Replace Method With Method Object to create new class ArticleView
public abstract class ArticleView
{
    protected Article article;

    protected abstract string Title { get; }
    protected abstract string Intro { get; }
    protected abstract string Body { get; }

    protected ArticleView(Article article)
    {
        this.article = article;
    }

    // 1.2 View method
    public string View()
    {
        return Title + Intro + Body;
    }
}

// 2.1 Extract a subclass from ArticleView
public class ArticleMarkdown: ArticleView
{
    // 3.1 break the View method to properties
    protected override string Title => "# " + article.Title + Environment.NewLine + Environment.NewLine;

    protected override string Intro => "> " + article.Intro + Environment.NewLine + Environment.NewLine;

    protected override string Body => article.Body + Environment.NewLine + Environment.NewLine;


    public ArticleMarkdown(Article article): base(article)
    {
    }
}

// 2.2 Extract a subclass from ArticleView
public class ArticleHtml: ArticleView
{
    // 3.2 break the View method to properties
    protected override string Title => "<h2>" + article.Title + "</h2>" + Environment.NewLine;

    protected override string Intro => "<blockquote>" + article.Intro + "</blockquote>" + Environment.NewLine;

    protected override string Body => "<p>" + article.Body + "</p>" + Environment.NewLine;

    public ArticleHtml(Article article): base(article)
    {
    }
}
```

- ###  Replace Inheritance with Delegation

Применяя вместо наследования делегирование, мы открыто заявляем,что используем делегируемый класс лишь частично. Мы управляем тем, какую часть интерфейса взять, а какую – игнорировать.
**Before:**

```csharp
public class Engine
{
    //…
    public double Fuel
    { get; set; }
    public double CV
    { get; set; }
}

public class Car: Engine
{
    // ...
    public string Brand    { get; set; }
    public string Model    { get; set; }
    public string Name => Brand + " " + Model + " (" + CV + "CV)";
}
```

**After:**

```csharp
public class Engine
{
    //…
    public double Fuel    { get; set; }
    public double CV      { get; set; }
}

//2. Remove Inheritance
public class Car
{
    // 1. Create filed with Base Class
    protected Engine _engine;

    public string Brand { get; set; }
    public string Model { get; set; }
    public string Name => Brand + " " + Model + " (" + _engine.CV + "CV)";

    public Car()
    {
        _engine = new Engine();
    }
}   
```

- ### Replace Delegation with Inheritance

**Before:**

```csharp

```

**After:**

```csharp

```
