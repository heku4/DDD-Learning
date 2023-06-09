## Observer

Observer - это поведенческий паттерн проектирования, который создаёт механизм подписки, позволяющий одним объектам следить и реагировать на события, происходящие в других объектах.

- IObservable: представляет наблюдаемый объект. Определяет три метода: AddObserver() (для добавления наблюдателя), RemoveObserver() (удаление набюдателя) и NotifyObservers() (уведомление наблюдателей)
- ConcreteObservable: конкретная реализация интерфейса IObservable. Определяет коллекцию объектов наблюдателей.
- IObserver: представляет наблюдателя, который подписывается на все уведомления наблюдаемого объекта. Определяет метод Update(), который вызывается наблюдаемым объектом для уведомления наблюдателя.
- ConcreteObserver: конкретная реализация интерфейса IObserver.

```csharp
public interface IObserver
{
    void Update(IObservable observable);
}

public interface IObservable
{
    void AddObserver(IObserver observer);
    void RemoveObserver(IObserver observer);
    void NotifyObservers();
}

public class ConcreteObservable : IObservable
{
    public int State { get; set; } = 0;
    private List<IObserver> _observers = new ();

    public void AddObserver(IObserver observer)
    {
        _observers.Add(observer);
    }

    public void RemoveObserver(IObserver observer)
    {
        _observers.Remove(observer);
    }

    public void NotifyObservers()
    {
        foreach (var observer in _observers)
        {
            observer.Update(this);
        }
    }

    public void DoBusinessLogic()
    {
        State = new Random().Next(0, 10);

        Thread.Sleep(15);

        NotifyObservers();
    }
}

class ConcreteObserverA : IObserver
{
    public void Update(IObservable observable)
    {
        if ((observable as ConcreteObservable).State < 3)
        {
            Console.WriteLine("ConcreteObserverA: Reacted to the event.");
        }
    }
}

class ConcreteObserverB : IObserver
{
    public void Update(IObservable observable)
    {
        if ((observable as ConcreteObservable).State == 0 || (observable as ConcreteObservable).State >= 2)
        {
            Console.WriteLine("ConcreteObserverB: Reacted to the event.");
        }
    }
}

class Program
{
    static void Main(string[] args)
    {
        var subject = new ConcreteObservable();
        var observerA = new ConcreteObserverA();
        subject.AddObserver(observerA);

        var observerB = new ConcreteObserverB();
        subject.AddObserver(observerB);

        subject.DoBusinessLogic();
        subject.DoBusinessLogic();

        subject.RemoveObserver(observerB);

        subject.DoBusinessLogic();
    }
}
```

**Преимущества**
- Издатели не зависят от конкретных классов подписчиков и наоборот.
- Вы можете подписывать и отписывать получателей на лету.
- Реализует принцип открытости/закрытости.

**Недостатки**
- Подписчики оповещаются в случайном порядке.