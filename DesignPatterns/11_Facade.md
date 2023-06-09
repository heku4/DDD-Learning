## Facade

Facade — это структурный паттерн проектирования, который предоставляет простой интерфейс к сложной системе классов, библиотеке или фреймворку. Фасад позволит определить одну точку взаимодействия между клиентом и системой. Можно ввести дополнительный фасад, чтобы не «захламлять» единственный фасад разнородной функциональностью. 

- Client взаимодействует с компонентами подсистемы
- Facade - непосредственно фасад, который предоставляет интерфейс клиенту для работы с компонентами
- Классы SubsystemA, SubsystemB, SubsystemC и т.д. являются компонентами сложной подсистемы, с которыми должен взаимодействовать клиент

```csharp
class TextEditor
{
    public void CreateCode()
    {
        Console.WriteLine("Write code");
    }
    public void Save()
    {
        Console.WriteLine("Save code");
    }
}
class Compiller
{
    public void Compile()
    {
        Console.WriteLine("Compile app");
    }
}
class CLR
{
    public void Execute()
    {
        Console.WriteLine("App is running");
    }
    public void Finish()
    {
        Console.WriteLine("App is finished");
    }
}

class VisualStudioFacade
{
    private TextEditor _textEditor;
    private Compiller _compiler;
    private CLR _clr;

    public VisualStudioFacade(TextEditor textEditor, Compiller compiler, CLR clr)
    {
        _textEditor = textEditor;
        _compiler = compiler;
        _clr = clr;
    }

    public void Start()
    {
        _textEditor.CreateCode();
        _textEditor.Save();
        _compiler.Compile();
        _clr.Execute();
    }

    public void Stop()
    {
        _clr.Finish();
    }
}

class SoftwareEngineer
{
    public void CreateApp(VisualStudioFacade facade)
    {
        facade.Start();
        facade.Stop();
    }
}

class Program
{
    static void Main(string[] args)
    {
        var textEditor = new TextEditor();
        var compiller = new Compiller();
        var clr = new CLR();
         
        var ide = new VisualStudioFacade(textEditor, compiller, clr);
        var engineer = new SoftwareEngineer();
        engineer.CreateApp(ide);
    }
}
```

**Преимущества**
- Изолирует клиентов от компонентов сложной подсистемы.
**Недостатки**
- Фасад рискует стать "God Object", привязанным ко всем классам программы.