## Composite

Composite - это структурный паттерн проектирования, который позволяет сгруппировать множество объектов в древовидную структуру, а затем работать с ней так, как будто это единичный объект.Паттерн имеет смысл только тогда, когда основная модель программы может быть структурирована в виде дерева.

- Component: определяет интерфейс для всех компонентов в древовидной структуре
- Composite (Container): представляет компонент, который может содержать другие компоненты и реализует механизм для их добавления и удаления
- Leaf: представляет отдельный компонент, который не может содержать другие компоненты

```csharp

abstract class Component // Component
{
    protected readonly string Name;
    protected int _size = 0;

    protected Component(string name)
    {
        this.Name = name;
    }

    public virtual void Print()
    {
        Console.WriteLine(Name);
    }

    public virtual int GetSize()
    {
        return _size;
    }
}
class Directory :Component // Composite (Container)
{
    private readonly List<Component> _components = new List<Component>();
 
    public Directory(string name)
        : base(name)
    {
    }
 
    public void Add(Component component)
    {
        _components.Add(component);
    }
 
    public void Remove(Component component)
    {
        _components.Remove(component);
    }
 
    public override void Print()
    {
        Console.WriteLine("Node " + Name);
        Console.WriteLine("Child node:");
        foreach (var component in _components)
        {
            component.Print();
        }
    }

    public override int GetSize()
    {
        var result = 0;
        foreach (var component in _components)
        {
             result += component.GetSize();
        }

        return result;
    }
}
 
class File : Component // Leaf
{
    public File(string name, int size) : base(name)
    {
        _size = size;
    }
}

class Program
{
    static void Main(string[] args)
    {
        var fileSystem = new Directory("FileSystem");
        
        var root = new Directory("Root");
        
        var pngFile = new File("12345.png", 100);
        var docxFile = new File("Document.docx", 320);
        
        root.Add(pngFile);
        root.Add(docxFile);
        
        fileSystem.Add(root);

        fileSystem.Print();
    }
}
```

**Преимущества**
- Упрощает архитектуру клиента при работе со сложным деревом компонентов.
- Облегчает добавление новых видов компонентов.

**Недостатки**
- Создаёт слишком общий дизайн классов.