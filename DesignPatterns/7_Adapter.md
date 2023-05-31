## Adapter

Adapter - структурный паттерн, предназначен для преобразования интерфейса одного класса в интерфейс другого. Благодаря реализации данного паттерна мы можем использовать вместе классы с несовместимыми интерфейсами.

- Target: представляет объекты, которые используются клиентом

- Client: использует объекты Target для реализации своих задач

- Adaptee: представляет адаптируемый класс, который нужно использовать у клиента вместо объектов Target

- Adapter: собственно адаптер, который позволяет работать с объектами Adaptee как с объектами Target.

Для реализации адаптера, ему нужно создать поле с Adaptee классом.

```csharp

// Target interface
interface ITransport 
{
    void Drive();
}

class Auto : ITransport
{
    public void Drive()
    {
        Console.WriteLine("Road moving");
    }
}

class Driver
{
    public void Travel(ITransport transport)
    {
        transport.Drive();
    }
}

// Adaptee interface
interface IAnimal
{
    void Move();
}

class Camel : IAnimal
{
    public void Move()
    {
        Console.WriteLine("Sand moving");
    }
}

// Adapter
class CamelToTransportAdapter : ITransport
{
    Camel _camel; // Adaptee class

    public CamelToTransportAdapter(Camel c)
    {
        _camel = c;
    }
 
    public void Drive()
    {
        _camel.Move();
    }
}

class Program
{
    static void Main(string[] args)
    {
        var driver = new Driver();
        var auto = new Auto();
        driver.Travel(auto);
        var camel = new Camel();
        
        ITransport camelTransport = new CamelToTransportAdapter(camel);
        driver.Travel(camelTransport);
 
        Console.Read();
    }
}

```

**Преимущества**
- Отделяет и скрывает от клиента подробности преобразования различных интерфейсов.

**Недостатки**
- Усложняет код программы из-за введения дополнительных классов.