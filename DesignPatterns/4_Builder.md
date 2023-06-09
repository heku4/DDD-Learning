## Builder

Builder - порождающий шаблон проектирования, который инкапсулирует создание объекта и позволяет разделить его на различные этапы.

**Участники**
- Product: представляет объект, который должен быть создан. В данном случае все части объекта заключены в списке parts.

- Builder: определяет интерфейс для создания различных частей объекта Product

- ConcreteBuilder: конкретная реализация Buildera. Создает объект Product и определяет интерфейс для доступа к нему

- Director: создает объект, используя объекты Builder

```csharp
using System.Text;

class Engine
{
    public int HorsePower { get; set; }
}

class Doors
{
    public int NumberOfDoors { get; set; }
}

class Paint
{
    public string Color { get; set; }
}
 
class Car
{
    public Engine CarEngine { get; set; }
    public Doors CarDoors { get; set; }
    public Paint CarPaint { get; set; }

    public override string ToString()
    {
        StringBuilder sb = new StringBuilder();
 
        if (CarEngine!= null)
            sb.Append($"Car with {CarEngine.HorsePower} HP \n");
        if (CarDoors != null)
            sb.Append($"with {CarDoors.NumberOfDoors} doors \n");
        if (CarPaint != null)
            sb.Append($"with {CarPaint.Color} color");
        return sb.ToString();
    }
}

abstract class CarBuilder
{
    public Car Car { get; private set; }
    public void CreateBread()
    {
        Car = new Car();
    }
    public abstract void SetEngine();
    public abstract void SetDoors();
    public abstract void SetColor();
}

class CarManufacturer //director
{
    public Car Make(CarBuilder carBuilder)
    {
        carBuilder.CreateBread();
        carBuilder.SetColor();
        carBuilder.SetDoors();
        carBuilder.SetEngine();
        return carBuilder.Car;
    }
}

class SportCarBuilder : CarBuilder
{

    public override void SetEngine()
    {
        Car.CarEngine.HorsePower = 200;
    }

    public override void SetDoors()
    {
        Car.CarDoors.NumberOfDoors = 2;
    }

    public override void SetColor()
    {
        Car.CarPaint.Color = "Red Metallic";
    }
}

class SedanBuilder : CarBuilder
{

    public override void SetEngine()
    {
        Car.CarEngine.HorsePower = 100;
    }

    public override void SetDoors()
    {
        Car.CarDoors.NumberOfDoors = 5;
    }

    public override void SetColor()
    {
        Car.CarPaint.Color = "White";
    }
}
public static class Program
{
    public static void Main()
    {
        var director = new CarManufacturer();
        var sportCarBuilder = new SportCarBuilder();
        var sedanBuilder = new SedanBuilder();
        var sportCar = director.Make(sportCarBuilder);
        var commonCar = director.Make(sedanBuilder);

        commonCar.ToString();
    }
}
```

**Преимущества**
- Позволяет создавать продукты пошагово.
- Позволяет использовать один и тот же код для создания различных продуктов.
- Изолирует сложный код сборки продукта от его основной бизнес-логики.

**Недостатки**
- Усложняет код программы из-за введения дополнительных
классов.
- Клиент будет привязан к конкретным классам строителей, так как в интерфейсе директора может не быть метода получения результата.