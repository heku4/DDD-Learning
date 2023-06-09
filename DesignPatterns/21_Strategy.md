## Startegy

Strategy - это поведенческий паттерн проектирования, который определяет семейство схожих алгоритмов и помещает каждый из них в собственный класс, после чего
алгоритмы можно взаимозаменять прямо во время исполнения программы.

- Интерфейс IStrategy, который определяет метод Algorithm(). Это общий интерфейс для всех реализующих его алгоритмов. Вместо интерфейса здесь также можно было бы использовать абстрактный класс.
- Классы ConcreteStrategy1 и ConcreteStrategy, которые реализуют интерфейс IStrategy, предоставляя свою версию метода Algorithm(). Подобных классов-реализаций может быть множество.
- Класс Context хранит ссылку на объект IStrategy и связан с интерфейсом IStrategy отношением агрегации.

```csharp
interface INavigation
{
    void BuildRoute(double posA, double posB);
}
 
class CarNavigation : INavigation
{
    public void BuildRoute(double posA, double posB)
    {
        Console.WriteLine($"From {posA} to {posB} by car");
    }
}
 
class BikeNavigation : INavigation
{
    public void BuildRoute(double posA, double posB)
    {
        Console.WriteLine($"From {posA} to {posB} by bike");
    }
}
class Navigator
{
    protected double PositionA;
    protected double PositionB;
 
    public Navigator(double startPos, double finishPos, INavigation mov)
    {
        PositionA = startPos;
        PositionB = finishPos;
        NavigationType = mov;
    }

    public INavigation NavigationType { private get; set; }
    public void Move()
    {
        NavigationType.BuildRoute(PositionA, PositionB);
    }
}

class Program
{
    static void Main(string[] args)
    {
        var navigator = new Navigator(1, 44, new CarNavigation());
        navigator.Move();
        navigator.NavigationType = new BikeNavigation();
        navigator.Move();
    }
}
```
**Преимущества**
- Горячая замена алгоритмов на лету.
- Изолирует код и данные алгоритмов от остальных классов.
- Уход от наследования к делегированию.
- Реализует принцип открытости/закрытости.

**Недостатки**
- Усложняет программу за счёт дополнительных классов.
- Клиент должен знать, в чём состоит разница между стратегиями, чтобы выбрать подходящую