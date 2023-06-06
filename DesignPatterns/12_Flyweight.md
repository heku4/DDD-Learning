## Flyweight

Flyweight - это структурный паттерн проектирования, который позволяет вместить бóльшее количество объектов в отведённую оперативную память. Он экономит память, разделяя общее состояние объектов между собой, вместо хранения одинаковых данных в каждом объекте.

Неизменяемые данные объекта принято называть «внутренним состоянием». Все остальные данные — это «внешнее состояние».
Паттерн Flyweight предлагает не хранить в классе внешнее состояние, а передавать его в те или иные методы через параметры.

- Flyweight: определяет интерфейс, через который приспособленцы-разделяемые объекты могут получать внешнее состояние или воздействовать на него

- ConcreteFlyweight: конкретный класс разделяемого приспособленца. Реализует интерфейс, объявленный в типе Flyweight, и при необходимости добавляет внутреннее состояние. Причем любое сохраняемое им состояние должно быть внутренним, не зависящим от контекста

- UnsharedConcreteFlyweight: еще одна конкретная реализация интерфейса, определенного в типе Flyweight, только теперь объекты этого класса являются неразделяемыми

- FlyweightFactory: фабрика Flyweight - создает объекты разделяемых Flyweight. Так как приспособленцы разделяются, то клиент не должен создавать их напрямую. Все созданные объекты хранятся в пуле. Если запрошенного приспособленца не оказалось в пуле, то фабрика создает его (или  инициализирует все значение).

- Client: использует объекты Flyweight. Может хранить внешнее состояние и передавать его в качестве аргументов в методы Flyweight


```csharp
abstract class House // Flyweight
{
    protected int Stages;
 
    public abstract void Build(int tenants);
}
 
class PanelHouse : House //Concrete Flyweight A
{
    public PanelHouse()
    {
        Stages = 16;
    }
 
    public override void Build(int tenants)
    {
        Console.WriteLine($"Built panel house with {Stages} stages. Tenants: {tenants}");
    }
}

class BrickHouse : House // Concrete Flyweight B
{
    public BrickHouse()
    {
        Stages = 5;
    }
 
    public override void Build(int tenants)
    {
        Console.WriteLine($"Built brick house with {Stages} stages. Tenants: {tenants}");
    }
}
 
class HouseFactory // Flyweight Factory
{
    Dictionary<string, House> houses = new Dictionary<string, House>();
    public HouseFactory()
    {
        houses.Add("Panel", new PanelHouse());
        houses.Add("Brick", new BrickHouse());
    }
 
    public House? GetHouse(string key)
    {
        if (houses.TryGetValue(key, out var house))
        {
            return  house;
        }
        
        return null;
    }
}

class Program
{
    static void Main(string[] args)
    {
        const int newComers = 37;
        const int tenants = 55;
 
        var houseFactory = new HouseFactory();
        for (var i = 0; i < 5;i++)
        {
            var panelHouse = houseFactory.GetHouse("Panel");
            if (panelHouse != null)
                panelHouse.Build(tenants);
        }
 
        for (var i = 0; i < 5; i++)
        {
            var brickHouse = houseFactory.GetHouse("Brick");
            if (brickHouse != null)
                brickHouse.Build(newComers);
        }
 
        Console.Read();
    }
}

```

**Преимущества**
- Экономит оперативную память.

**Недостатки**
- Расходует процессорное время на поиск/вычисление контекста.
- Усложняет код программы из-за введения множества дополнительных классов.