## Abstract Factory

Это порождающий паттерн, который позволяет создавать семейства связанных объектов, не привязываясь к конкретным классам создаваемых объектов.

Все вариации одного и того же объекта должны жить в одной иерархии классов

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

public interface IPassengerTransport
{
    public int Capacity { get; }
    public int Seats { get; }

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
        Console.WriteLine("Deliver - CargoPlane");
    }
}

public class PassengerPlane : IPassengerTransport
{
    public int Capacity { get; }
    public int Seats { get; }

    public PassengerPlane(int capacity, int seats)
    {
        Capacity = capacity;
        Seats = seats;
    }

    public void Deliver()
    {
        Console.WriteLine("Deliver - PassengerPlane");
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
        Console.WriteLine("Deliver - CargoShip");
    }
}

public class CruiseShip: IPassengerTransport
{
    public int Capacity { get; }
    public int Seats { get; }

    public CruiseShip(int capacity, int seats)
    {
        Capacity = capacity;
        Seats = seats;
    }
    public void Deliver()
    {
        Console.WriteLine("Deliver - CruiseShip");
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
public class Bus : IPassengerTransport
{
    public int Capacity { get; }
    public int Seats { get; }

    public Bus(int capacity, int seats)
    {
        Capacity = capacity;
        Seats = seats;
    }

    public void Deliver()
    {
        Console.WriteLine("Deliver - Bus");
    }
}
public interface ITransportFactory
{
    public ITransport CreateCargoTransport();
    public IPassengerTransport CreatePassengerTransport();
}

public class PlaneFactory : ITransportFactory
{
    public ITransport CreateCargoTransport()
    {
        return new Plane(50);
    }

    public IPassengerTransport CreatePassengerTransport()
    {
        return new PassengerPlane(5, 100);
    }
}

public class TruckFactory : ITransportFactory
{
    public ITransport CreateCargoTransport()
    {
        return new Truck(50);
    }

    public IPassengerTransport CreatePassengerTransport()
    {
        return new Bus(3, 50);
    }
}

public class ShipFactory : ITransportFactory
{
    public ITransport CreateCargoTransport()
    {
        return new Ship(1000);
    }

    public IPassengerTransport CreatePassengerTransport()
    {
        return new CruiseShip(100, 1000);
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
                throw new Exception("Unknown type");
        }
    }
}

public static class Program
{
    public static void Main()
    {
        var transportFactory = new TransportFactory();
        var liner = transportFactory.GetFactory(TransportType.Ship).CreatePassengerTransport();
        
        liner.Deliver();
    }
}
```

**Преимущества**
 - Скрыты детали реализации создания объектов от клиентского кода.
 - Метод создания объектов содержаться в одном месте. 
 - Упрощает добавление новых продуктов.
 - Реализует принцип открытости-закрытости.

 **Недостатки**
 - Приводит к созданию больших параллельных иерархий клаасов.