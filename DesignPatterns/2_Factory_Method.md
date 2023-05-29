## Factory Method

Порождающий паттерн. Является базовым для всех порождаающих паттернов. Используется, если нужно создать доменный объект в runtime - то есть когда сам объект определяется при выполнении программы. Либо этот домен объекта не известен до конца и может меняться в течение жизни всего домена. 
Позволяет задать базовую логику и переиспользовать функционал.

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

public interface ITransportFactory
{
    public ITransport Create();
}

public class PlaneFactory : ITransportFactory
{
    public ITransport Create()
    {
        return new Plane(50);
    }
}

public class TruckFactory : ITransportFactory
{
    public ITransport Create()
    {
        return new Truck(50);
    }
}

public class CartFactory : ITransportFactory
{
    public ITransport Create()
    {
        return new Cart(1);
    }
}

public class ShipFactory : ITransportFactory
{
    public ITransport Create()
    {
        return new CargoShip(1000);
    }
}

public class TransportFactory
{
    public ITransportFactory GetFactory(TransportType type)
    {
        switch (type)
        {
            case TransportType.Plane:
                return new PlaneFactory();
            case TransportType.Truck:
                return new TruckFactory();
            case TransportType.Ship:
                return new ShipFactory();
            default:
                return new CartFactory();
        }
    }
}

public static class Program
{
    public static void Main()
    {
        var transportFactory = new TransportFactory();
        var ship = transportFactory.GetFactory(TransportType.Ship).Create();
        
        ship.Deliver();
    }
}
```

**Преимущества**
 - Скрыты детали реализации создания объектов от клиентского кода.
 - Метод создания объектов содержаться в одном месте. 
 - Упрощает добавление новых продуктов.
 - Реализует принцип открытости-закрытости.

 **Недостатки**
 - Может привести к созданию больших параллельных иерархий клаасов.