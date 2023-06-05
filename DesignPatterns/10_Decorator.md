## Decorator

Decorator - это структурный паттерн проектирования, который позволяет динамически добавлять объектам новую функциональность, оборачивая их в полезные «обёртки». Деократор построен наприцнипе аггрегации и композиции. Этот паттерн выбирается когда надо динамически добавлять к объекту новые функциональные возможности, при этом данные возможности могут быть сняты с объекта.Когда применение наследования неприемлемо.

- Component: абстрактный класс, который определяет интерфейс для наследуемых объектов

- ConcreteComponent: конкретная реализация компонента, в которую с помощью декоратора добавляется новая функциональность

- Decorator: собственно декоратор, реализуется в виде абстрактного класса и имеет тот же базовый класс, что и декорируемые объекты. Поэтому базовый класс Component должен быть по возможности легким и определять только базовый интерфейс.
Класс декоратора также хранит ссылку на декорируемый объект в виде объекта базового класса Component и реализует связь с базовым классом как через наследование, так и через отношение агрегации.

- Классы ConcreteDecoratorA и ConcreteDecoratorB представляют дополнительные функциональности, которыми должен быть расширен объект ConcreteComponent.

```csharp
abstract class Car // Component
{
    public string Model { get; private set; }
    public Car(string model)
    {
        Model = model;
    }

    public abstract int GetCost();
}


class BmwCar: Car // Concrete Component
{
    public BmwCar() : base("BMW X5 ")
    {
    }

    public override int GetCost()
    {
        return 0;
    }
}

abstract class CarDecorator: Car // Decorator
{
    protected Car Car;

    protected CarDecorator(string model, Car car) : base(model)
    {
        Car = car;
    }
}

class TurboEngine: CarDecorator // Concrete Decorator A
{
    public TurboEngine(Car car) : base(car.Model + "turbo", car)
    {
        
    }

    public override int GetCost()
    {
        return 1000;
    }
}

class LuxurySeats: CarDecorator // Concrete Decorator B
{
    public LuxurySeats(Car car) : base(car.Model + "leather seats", car)
    {
        
    }

    public override int GetCost()
    {
        return 10000;
    }
}

class Program
{
    static void Main(string[] args)
    {
        var x5Model = new BmwCar();
        var turboCar = new TurboEngine(x5Model);
        var luxuryCar = new LuxurySeats(x5Model);
    }
}
```

**Преимущества**
- Большая гибкость, чем у наследования.
- Позволяет добавлять обязанности на лету.
- Можно добавлять несколько новых обязанностей сразу.
- Позволяет иметь несколько мелких объектов вместо одного объекта на все случаи жизни.
**Недостатки**
- Трудно конфигурировать многократно обёрнутые объекты.
- Обилие крошечных классов.