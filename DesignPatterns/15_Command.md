## Command

Command - это поведенческий паттерн проектирования, который превращает запросы в объекты, позволяя передавать их как аргументы при вызове методов, ставить запросы в очередь, логировать их, а также поддерживать отмену операций.

- Command: интерфейс, представляющий команду. Обычно определяет метод Execute() для выполнения действия, а также нередко включает метод Undo(), реализация которого должна заключаться в отмене действия команды
- ConcreteCommand: конкретная реализация команды, реализует метод Execute(), в котором вызывается определенный метод, определенный в классе Receiver
- Receiver: получатель команды. Определяет действия, которые должны выполняться в результате запроса.
- Invoker: инициатор команды - вызывает команду для выполнения определенного запроса
- Client: клиент - создает команду и устанавливает ее получателя с помощью метода SetCommand()

```csharp
class Program
{
    static void Main(string[] args)
    {
        var remote = new Remote();
        var tv = new TV();
        remote.SetCommand(new TVOnCommand(tv));
        remote.PressButton();
        remote.PressUndo();
         
        Console.Read();
    }
}
 
interface ICommand // Command
{
    void Execute();
    void Undo();
}
 

class TV // Receiver
{ 
    public void On()
    {
        Console.WriteLine("TV is ON");
    }
 
    public void Off()
    {
        Console.WriteLine("TV IS OFF");
    }
}
 
class TVOnCommand : ICommand // Concrete command
{
    TV tv;
    public TVOnCommand(TV tvSet)
    {
        tv = tvSet;
    }
    public void Execute()
    {
        tv.On();
    }
    public void Undo()
    {
        tv.Off();
    }
}

class Remote // Invoker
{
    ICommand command;
 
    public Remote() { }
 
    public void SetCommand(ICommand com)
    {
        command = com;
    }
 
    public void PressButton()
    {
        command.Execute();
    }
    public void PressUndo()
    {
        command.Undo();
    }
}
```

**Преимущества**
- Убирает прямую зависимость между объектами, вызывающими операции, и объектами, которые их непосредственно выполняют.
- Позволяет реализовать простую отмену и повтор операций.
- Позволяет реализовать отложенный запуск операций.
- Позволяет собирать сложные команды из простых.
- Реализует принцип открытости/закрытости.

**Недостатки**
- Усложняет код программы из-за введения множества дополнительных классов.