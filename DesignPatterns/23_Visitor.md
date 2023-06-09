## Visitor

Visitor - это поведенческий паттерн проектирования, который позволяет добавлять в программу новые операции, не изменяя классы объектов, над которыми эти операции могут выполняться. При использовании паттерна Посетитель определяются две иерархии классов: одна для элементов, для которых надо определить новую операцию, и вторая иерархия для посетителей, описывающих данную операцию.

- Visitor: интерфейс посетителя, который определяет метод Visit() для каждого объекта Element
- ConcreteVisitor1 / ConcreteVisitor2: конкретные классы посетителей, реализуют интерфейс, определенный в Visitor.
- Element: определяет метод Accept(), в котором в качестве параметра принимается объект Visitor
- ElementA / ElementB: конкретные элементы, которые реализуют метод Accept()
- ObjectStructure: некоторая структура, которая хранит объекты Element и предоставляет к ним доступ. Это могут быть и простые списки, и сложные составные структуры в виде деревьев

```csharp
class Program
{
    static void Main(string[] args)
    {
        var structure = new Bank();
        structure.Add(new Person { Name = "John Travolta", Number = "82184931" });
        structure.Add(new Company {Name="Microsoft", RegNumber="123455", Number="3424131445"});
        structure.Accept(new HtmlVisitor());
        structure.Accept(new XmlVisitor());
 
        Console.Read();
    }
}
 
interface IVisitor // Visitor interface
{
    void VisitPersonAcc(Person acc);
    void VisitCompanyAc(Company acc);
}

class HtmlVisitor : IVisitor // ConcreteVisitor1
{
    public void VisitPersonAcc(Person acc)
    {
        string result = "<table><tr><td>Свойство<td><td>Значение</td></tr>";
        result += "<tr><td>Name<td><td>" + acc.Name + "</td></tr>";
        result += "<tr><td>Number<td><td>" + acc.Number + "</td></tr></table>";
        Console.WriteLine(result);
    }
 
    public void VisitCompanyAc(Company acc) 
    {
        string result = "<table><tr><td>Свойство<td><td>Значение</td></tr>";
        result += "<tr><td>Name<td><td>" + acc.Name + "</td></tr>";
        result += "<tr><td>RegNumber<td><td>" + acc.RegNumber + "</td></tr>";
        result += "<tr><td>Number<td><td>" + acc.Number + "</td></tr></table>";
        Console.WriteLine(result);
    }
}

class XmlVisitor : IVisitor //ConcreteVisitor2
{
    public void VisitPersonAcc(Person acc)
    {
        string result = "<Person><Name>"+acc.Name+"</Name>"+
            "<Number>"+acc.Number+"</Number><Person>";
        Console.WriteLine(result);
    }
 
    public void VisitCompanyAc(Company acc)
    {
        string result = "<Company><Name>" + acc.Name + "</Name>" + 
            "<RegNumber>" + acc.RegNumber + "</RegNumber>" + 
            "<Number>" + acc.Number + "</Number><Company>";
        Console.WriteLine(result);
    }
}
 
class Bank // ObjectStructure
{
    List<IAccount> _accounts = new List<IAccount>();
    public void Add(IAccount acc)
    {
        _accounts.Add(acc);
    }

    public void Remove(IAccount acc)
    {
        _accounts.Remove(acc);
    }

    public void Accept(IVisitor visitor)
    {
        foreach (IAccount acc in _accounts)
            acc.Accept(visitor);
    }
}
 
interface IAccount //Element 
{
    void Accept(IVisitor visitor);
}
 
class Person : IAccount // ElementA
{
    public string Name { get; set; }
    public string Number { get; set; }
 
    public void Accept(IVisitor visitor)
    {
        visitor.VisitPersonAcc(this);
    }
}
 
class Company : IAccount // ElementB
{
    public string Name { get; set; }
    public string RegNumber { get; set; }
    public string Number { get; set; }
 
    public void Accept(IVisitor visitor)
    {
        visitor.VisitCompanyAc(this);
    }
}
```

**Преимущества**
- Упрощает добавление операций, работающих со сложными структурами объектов.
- Объединяет родственные операции в одном классе.
- Посетитель может накапливать состояние при обходе структуры элементов.

**Недостатки**
- Паттерн не оправдан, если иерархия элементов часто меняется.
- Может привести к нарушению инкапсуляции элементов.