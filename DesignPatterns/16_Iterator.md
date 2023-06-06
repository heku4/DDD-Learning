## Iterator

Iterator — это поведенческий паттерн проектирования, который даёт возможность последовательно обходить элементы составных объектов, не раскрывая их внутреннего представления.

- Iterator: определяет интерфейс для обхода составных объектов
- Aggregate: определяет интерфейс для создания объекта-итератора
- ConcreteIterator: конкретная реализация итератора для обхода объекта Aggregate. Для фиксации индекса текущего перебираемого элемента использует целочисленную переменную _current
- ConcreteAggregate: конкретная реализация Aggregate. Хранит элементы, которые надо будет перебирать
- Client: использует объект Aggregate и итератор для его обхода

```csharp
class Program
{
    static void Main(string[] args)
    {
        var library = new Library();
        var reader = new Reader();
        reader.SeeBooks(library);
 
        Console.Read();
    }
}

class Reader
{
    public void SeeBooks(IBookAggregator bookAggregator)
    {
        var iterator = bookAggregator.CreateIterator();
        while(iterator.HasNext())
        {
            var book = iterator.Next();
            Console.WriteLine(book.Name);
        }
    }
}

public class Book
{
    public string Name { get; set; }
}

public interface IBookIterator
{
    bool HasNext();

    Book Next();
}

public interface IBookAggregator
{
    IBookIterator CreateIterator();
    int Count { get; }
    Book this[int index] { get;}
}

class Library : IBookAggregator
{
    private Book[] books;
    public int Count => books.Length;

    public Book this[int index] => books[index];
    public Library()
    {
        books = new Book[]
        {
            new Book {Name="War and Peace"},
            new Book {Name="Fathers and sons"},
            new Book {Name="The Cherry Orchard"}
        };
    }

    public IBookIterator CreateIterator()
    {
        return new LibraryIterator(this);
    }
}

class LibraryIterator : IBookIterator
{
    IBookAggregator aggregate;
    int _index = 0;
    
    public LibraryIterator(IBookAggregator a)
    {
        aggregate = a;
    }

    public bool HasNext()
    {
        return _index < aggregate.Count;
    }

    public Book Next()
    {
        return aggregate[_index++];
    }
}

```
**Преимущества**
- Упрощает классы хранения данных.
- Позволяет реализовать различные способы обхода структуры данных.
- Позволяет одновременно перемещаться по структуре данных в разные стороны.

**Недостатки**
- Не оправдан, если можно обойтись простым циклом.