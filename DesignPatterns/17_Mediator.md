## Mediator

Mediator - это поведенческий паттерн проектирования, который позволяет уменьшить связанность множества классов между собой, благодаря перемещению этих связей в один класс-посредник.

- Mediator: представляет интерфейс для взаимодействия с объектами Colleague
- Colleague: представляет интерфейс для взаимодействия с объектом Mediator
- ConcreteColleague1 и ConcreteColleague2: конкретные классы коллег, которые обмениваются друг с другом через объект Mediator
- ConcreteMediator: конкретный посредник, реализующий интерфейс типа Mediator

```csharp
class Program
{
    static void Main(string[] args)
    {
        var mediator = new ManagerMediator();
        var customer = new CustomerColleague(mediator);
        var programmer = new ProgrammerColleague(mediator);
        var tester = new QaColleague(mediator);
        mediator.Customer = customer;
        mediator.Programmer = programmer;
        mediator.Tester = tester;
        customer.Send("New order. Need a new program");
        programmer.Send("Program is done. Need to be tested");
        tester.Send("Program is tested. Ready for productive.");
 
        Console.Read();
    }
}
 
abstract class Mediator // Mediator
{
    public abstract void Send(string msg, Colleague colleague);
}

abstract class Colleague // Colleague
{
    protected Mediator mediator;
 
    public Colleague(Mediator mediator)
    {
        this.mediator = mediator;
    }
 
    public virtual void Send(string message)
    {
        mediator.Send(message, this);
    }
    public abstract void Notify(string message);
}

class CustomerColleague : Colleague // ConcreteColleague1
{
    public CustomerColleague(Mediator mediator)
        : base(mediator)
    { }
 
    public override void Notify(string message)
    {
        Console.WriteLine("Message to customer: " + message);
    }
}

class ProgrammerColleague : Colleague  // ConcreteColleague2
{
    public ProgrammerColleague(Mediator mediator)
        : base(mediator)
    { }
 
    public override void Notify(string message)
    {
        Console.WriteLine("Message to programmer: " + message);
    }
}

class QaColleague : Colleague // ConcreteColleague3
{
    public QaColleague(Mediator mediator)
        : base(mediator)
    { }
 
    public override void Notify(string message)
    {
        Console.WriteLine("Message to QA: " + message);
    }
}

class ManagerMediator : Mediator // ConcreteMediator
{
    public Colleague Customer { get; set; }
    public Colleague Programmer { get; set; }
    public Colleague Tester { get; set; }
    public override void Send(string msg, Colleague colleague)
    {
        if (Customer == colleague)
            Programmer.Notify(msg);
        
        else if (Programmer == colleague)
            Tester.Notify(msg);

        else if (Tester == colleague)
            Customer.Notify(msg);
    }
}
```
**Преимущества**
- Устраняет зависимости между компонентами, позволяя повторно их использовать.
- Упрощает взаимодействие между компонентами.
- Централизует управление в одном месте.

**Недостатки**
- Посредник может сильно раздуться