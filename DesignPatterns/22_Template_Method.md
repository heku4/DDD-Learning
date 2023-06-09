## Template Method

Template Method - это поведенческий паттерн проектирования, который определяет скелет алгоритма, перекладывая ответственность за некоторые его шаги на подклассы. Паттерн позволяет подклассам переопределять шаги алгоритма, не меняя его общей структуры.

- AbstractClass: определяет шаблонный метод TemplateMethod(), который реализует алгоритм. Алгоритм может состоять из последовательности вызовов других методов, часть из которых может быть абстрактными и должны быть переопределены в классах-наследниках. При этом сам метод TemplateMethod(), представляющий структуру алгоритма, переопределяться не должен.
- ConcreteClass: подкласс, который может переопределять различные методы родительского класса.

```csharp

abstract class House
{
    public void CreateHouse() // Template Method
    {
        MakeFoundation();
        MakeWalls();
        MakeRoof();
    }
    
    public virtual void MakeFoundation()
    {
        Console.WriteLine("Concrete foundation");
    }
    
    public abstract void MakeWalls();
    public abstract void MakeRoof();
}
     
class WoodenHouse : House // ConcreteClass A
{
    public override void MakeWalls()
    {
        Console.WriteLine("Wooden walls");
    }
 
    public override void MakeRoof()
    {
        Console.WriteLine("Common roof");
    }
}
 
class BrickHouse : House // ConcreteClass B
{
    public override void MakeWalls()
    {
        Console.WriteLine("Brick walls");
    }
 
    public override void MakeRoof()
    {
        Console.WriteLine("Tile roof");
    }
}

class Program
{
    static void Main(string[] args)
    {
        var woodenHouse = new WoodenHouse();
        var brickHouse = new BrickHouse();
 
        woodenHouse.CreateHouse();
        brickHouse.CreateHouse();
    }
}
```

**Преимущества**
- Облегчает повторное использование кода.
**Недостатки**
- Вы жёстко ограничены скелетом существующего алгоритма.
- Вы можете нарушить принцип LSP, изменяя базовое поведение одного из шагов алгоритма через подкласс.
- С ростом количества шагов шаблонный метод становится слишком сложно поддерживать.