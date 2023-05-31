## Bridge

Bridge - это структурный паттерн проектирования, который разделяет один или несколько классов на две отдельные иерархии — абстракцию и реализацию, позволяя изменять их независимо друг от друга. Паттерн Bridge предлагает заменить наследование агрегацией или композицией. Вместо того чтобы создавать классы цветных фигур (YellowSquare, Yeelow Circle, Red Circle, Red Square), отддельные типы свойств выделить в отдельную иерархию и ссылаться на объект этой иерархии.

- Abstraction: определяет базовый интерфейс и хранит ссылку на объект Implementor. Выполнение операций в Abstraction делегируется методам объекта Implementor

- RefinedAbstraction: уточненная абстракция, наследуется от Abstraction и расширяет унаследованный интерфейс

- Implementor**: определяет базовый интерфейс для конкретных реализаций. Как правило, Implementor определяет только примитивные операции. Более сложные операции, которые базируются на примитивных, определяются в Abstraction

- ConcreteImplementorA и ConcreteImplementorB: конкретные реализации, которые унаследованы от Implementor

- **Client**: использует объекты Abstraction

```csharp
public interface IDevice //Implementor
{
    public bool IsEnabled();
    public void Enable();
    public void Disable();
    public int GetVolume();
    public void SetVolume(int level);
    public int GetChannel();
    public void SetChannel(int channel);
}


abstract class Remote //Abstraction
{
    private readonly IDevice _device; //reference to Implementor

    protected Remote(IDevice device)
    {
        _device = device;
    }

    public virtual void TogglePower()
    {
        if (_device.IsEnabled())
        {
            _device.Enable();
            return;
        }
        
        _device.Disable();
    }

    public virtual void VolumeUp()
    {
        var level = _device.GetVolume();
        _device.SetVolume(level + 1);
    }

    public virtual void ChannelUp()
    {
        var channel = _device.GetChannel();
        _device.SetChannel(channel + 1);
    }
}

public class Tv: IDevice // ConcreteImplementationB
{
   
    // ...
}

class CommonRemote : Remote //RefinedAbstraction
{
    public CommonRemote(IDevice device) : base(device)
    {
    }
}

public class Radio : IDevice // ConcreteImplementationB
{
    // ...
   
}

class Program
{
    static void Main(string[] args)
    {
        var tv = new Tv();
        var remote = new CommonRemote(tv);
        
        remote.TogglePower();

        var radio = new Radio();

        remote = new CommonRemote(radio);

        Console.Read();
    }
}

```

**Преимущества**
- Позволяет строить платформо-независимые программы.
- Скрывает лишние или опасные детали реализации от клиентского кода.
- Реализует принцип открытости/закрытости.

**Недостатки**
-Усложняет код программы из-за введения дополнительных классов.
