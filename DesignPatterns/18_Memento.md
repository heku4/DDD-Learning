## Memento

Memento - это поведенческий паттерн проектирования, который позволяет сохранять и восстанавливать прошлые состояния объектов, не раскрывая подробностей их реализации. Иными словами - позволяет выносить внутреннее состояние объекта за его пределы для последующего возможного восстановления объекта без нарушения принципа инкапсуляции.

- Memento: хранитель, который сохраняет состояние объекта Originator и предоставляет полный доступ только этому объекту Originator
- Originator: создает объект хранителя для сохранения своего состояния
- Caretaker: выполняет только функцию хранения объекта Memento, в то же время у него нет полного доступа к хранителю и никаких других операций над хранителем, кроме собственно сохранения, он не производит

```csharp

class Originator // originator
{
    private string _state;

    public Originator(string state)
    {
        _state = state;
    }

    public string GetText()
    {
        return _state;
    }

    public void TypeText(string text)
    {
        _state += text;
    }

    public IMemento Save()
    {
        return new ConcreteMemento(_state);
    }

    public void Restore(IMemento memento)
    {
        _state = memento.GetState();
    }
}

public interface IMemento // Memento
{
    string GetName();

    string GetState();

    DateTime GetDate();
}

class ConcreteMemento : IMemento // ConcreteMemento
{
    private string _state;

    private DateTime _date;

    public ConcreteMemento(string state)
    {
        _state = state;
        _date = DateTime.Now;
    }

    public string GetState()
    {
        return _state;
    }
    public string GetName()
    {
        return $"State from: {_date}";
    }

    public DateTime GetDate()
    {
        return _date;
    }
}

class Caretaker //Caretaker
{
    private List<IMemento> _mementos = new ();

    private Originator _originator = null;

    public Caretaker(Originator originator)
    {
        _originator = originator;
    }

    public void Backup()
    {
        _mementos.Add(_originator.Save());
    }

    public void Undo()
    {
        if (_mementos.Count == 0)
        {
            return;
        }

        var memento = _mementos.Last();
        _mementos.Remove(memento);
        

        try
        {
            _originator.Restore(memento);
        }
        catch (Exception)
        {
            Undo();
        }
    }

    public void ShowHistory()
    {
        foreach (var memento in _mementos)
        {
            Console.WriteLine(memento.GetName());
        }
    }
}

class Program
{
    static void Main(string[] args)
    {
        var originator = new Originator(string.Empty);
        var caretaker = new Caretaker(originator);

        caretaker.Backup();
        originator.TypeText("Happy ");

        caretaker.Backup();
        originator.TypeText("New ");

        caretaker.Backup();
        originator.TypeText("Year");

        Console.WriteLine();
        caretaker.ShowHistory();
        
        Console.WriteLine(originator.GetText());

        Console.WriteLine("\nRollback\n");
        caretaker.Undo();
        Console.WriteLine(originator.GetText());
        
        Console.WriteLine("\nRollback #2\n");
        caretaker.Undo();
        Console.WriteLine(originator.GetText());
        
        Console.WriteLine();
    }
}
```

**Преимущества**
- Не нарушает инкапсуляции исходного объекта.
- Упрощает структуру исходного объекта. Ему не нужно хранить историю версий своего состояния

**Недостатки**
- Требует много памяти, если клиенты слишком часто создают снимки.
- Может повлечь дополнительные издержки памяти, если объекты, хранящие историю, не освобождают ресурсы, занятые устаревшими снимками.
- В некоторых языках (например, PHP, Python, JavaScript) сложно гарантировать, чтобы только исходный объект имел доступ к состоянию снимка.