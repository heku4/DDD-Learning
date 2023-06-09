## State

State - это поведенческий паттерн проектирования, который позволяет объектам менять поведение в зависимости от своего состояния. Извне создаётся впечатление, что изменился класс объекта.

- State: определяет интерфейс состояния
- StateA и StateB - конкретные реализации состояний
- Context: представляет объект, поведение которого должно динамически изменяться в соответствии с состоянием. Выполнение же конкретных действий делегируется объекту состояния

```csharp
class Water
{
    public IWaterState State { get; set; }
 
    public Water(IWaterState ws)
    {
        State = ws;
    }
 
    public void Heat()
    {
        State.Heat(this);
    }
    public void Frost()
    {
        State.Frost(this);
    }
}
 
interface IWaterState
{
    void Heat(Water water);
    void Frost(Water water);
}
 
class SolidWaterState : IWaterState
{
    public void Heat(Water water)
    {
        Console.WriteLine("From ice to liquid");
        water.State = new LiquidWaterState();
    }
 
    public void Frost(Water water)
    {
        Console.WriteLine("Frost processing");
    }
}
class LiquidWaterState : IWaterState
{
    public void Heat(Water water)
    {
        Console.WriteLine("From liquid to vapor");
        water.State = new VaporWaterState();
    }
 
    public void Frost(Water water)
    {
        Console.WriteLine("From liquid to ice");
        water.State = new SolidWaterState();
    }
}
class VaporWaterState : IWaterState
{
    public void Heat(Water water)
    {
        Console.WriteLine("Heat up the vapor");
    }
 
    public void Frost(Water water)
    {
        Console.WriteLine("Cool down vapor to liquid");
        water.State = new LiquidWaterState();
    }
}

class Program
{
    static void Main(string[] args)
    {
        Water water = new Water(new LiquidWaterState());
        water.Heat();
        water.Frost();
        water.Frost();
    }
}
```

**преимущества**
- Избавляет от множества больших условных операторов машины состояний.
- Концентрирует в одном месте код, связанный с определённым состоянием.
- Упрощает код контекста.

**Недостатки**
- Может неоправданно усложнить код, если состояний мало и они редко меняются.