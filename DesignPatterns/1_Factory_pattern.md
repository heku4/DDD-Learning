## Factory

Порождающий паттерн. Простейшая фабрика - это объект для создания других объектов. В простой фабрике имеется класс, который имеет метод, создающий отдельные сущности, на основе вводимых данных.

```csharp

public enum TransportType
{
    Plane,
    Truck,
    Ship
}

public interface ITransport
{
    public int Capacity { get; }

    public void Deliver();
}

public class Plane : ITransport
{
    public int Capacity { get; }

    public Plane(int capacity)
    {
        Capacity = capacity;
    }

    public void Deliver()
    {
        Console.WriteLine("Deliver - Plane");
    }
}

public class Cart : ITransport
{
    public int Capacity { get; }

    public Cart(int capacity)
    {
        Capacity = capacity;
    }

    public void Deliver()
    {
        Console.WriteLine("Deliver - Cart");
    }
}

public class Ship : ITransport
{
    public int Capacity { get; }

    public Ship(int capacity)
    {
        Capacity = capacity;
    }

    public void Deliver()
    {
        Console.WriteLine("Deliver - Ship");
    }
}


public class Truck : ITransport
{
    public int Capacity { get; }

    public Truck(int capacity)
    {
        Capacity = capacity;
    }

    public void Deliver()
    {
        Console.WriteLine("Deliver - Truck");
    }
}

public class TransportFactory
{
    public ITransport Create(TransportType type)
    {
        switch (type)
        {
            case TransportType.Plane:
                return new Plane(50);
            case TransportType.Truck:
                return new Truck(10);
            case TransportType.Ship:
                return new Ship(1000);
            default:
                return new Cart(1);
        }
    }
}

public static class Program
{
    public static void Main()
    {
        var transportFactory = new TransportFactory();
        var ship = transportFactory.Create(TransportType.Ship);
        
        ship.Deliver();
    }
}

```

**Преимущества**
 - Скрыты детали реализации создания объектов от клиентского кода.
 - Метод создания объектов содержаться в одном месте. Будущие изменения будут проведены проще.

 **Недостатки**
 - Добавление нового типа продукта влечет за собой изменение метода создания продукта. В целом, это нарушает OCP.