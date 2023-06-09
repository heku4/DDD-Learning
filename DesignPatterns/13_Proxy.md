## Proxy

Proxy - это структурный паттерн проектирования, который позволяет подставлять вместо реальных объектов специальные объекты-заменители. Эти объекты перехватывают вызовы к оригинальному объекту, позволяя сделать что-то до или после передачи вызова оригиналу.

- Subject: определяет общий интерфейс для Proxy и RealSubject. Поэтому Proxy может использоваться вместо RealSubject

- RealSubject: представляет реальный объект, для которого создается прокси

- Proxy: заместитель реального объекта. Хранит ссылку на реальный объект, контролирует к нему доступ, может управлять его созданием и удалением. При необходимости Proxy переадресует запросы объекту RealSubject

- Client: использует объект Proxy для доступа к объекту RealSubject

```csharp
using Microsoft.EntityFrameworkCore;

class Program
{
    static void Main(string[] args)
    {
        using(IBook book = new BookStoreProxy())
        {
            
            var page1 = book.GetPage(1);
            Console.WriteLine(page1.Text);
            
            var page2 = book.GetPage(2);
            Console.WriteLine(page2.Text);
            
            // get first page from Proxy cahce
            page1 = book.GetPage(1);
            Console.WriteLine(page1.Text);
        }
             
        Console.Read();
    }
}

class Page // table
{
    public int Id { get; set; }
    public int Number { get; set; }
    public string Text { get; set; }
}

class PageContext : DbContext //EF
{
    public DbSet<Page> Pages { get; set; }
}
 
interface IBook : IDisposable // Subject
{
    Page GetPage(int number);
}
 
class BookStore : IBook // RealSubject
{
    PageContext db;
    public BookStore()
    {
        db = new PageContext();
    }

    public Page GetPage(int number)
    {
        return db.Pages.FirstOrDefault(p => p.Number == number);
    }
 
    public void Dispose()
    {
        db.Dispose();
    }
}
 
class BookStoreProxy : IBook // Proxy
{
    readonly List<Page> _pages;
    BookStore _bookStore; // reference to Real Subject

    public BookStoreProxy()
    {
        _pages=new List<Page>();
    }

    public Page GetPage(int number)
    {
        var page = _pages.FirstOrDefault(p=>p.Number==number);
        if (page == null)
        {
            if (_bookStore == null)
                _bookStore = new BookStore();
            page= _bookStore.GetPage(number);
            _pages.Add(page);
        }
        
        return page;
    }
 
    public void Dispose()
    {
        if(_bookStore!=null)
            _bookStore.Dispose();
    }
}
```

Когда использовать прокси?
- Когда надо осуществлять взаимодействие по сети, а объект-проси должен имитировать поведения объекта в другом адресном пространстве. Использование прокси позволяет снизить накладные издержки при передачи данных через сеть (remote proxies)

- Когда нужно управлять доступом к ресурсу, создание которого требует больших затрат. Реальный объект создается только тогда, когда он действительно может понадобится, а до этого все запросы к нему обрабатывает прокси-объект (virtual proxies)

- Когда необходимо разграничить доступ к вызываемому объекту в зависимости от прав вызывающего объекта (protection proxies)

- Когда нужно вести подсчет ссылок на объект или обеспечить потокобезопасную работу с реальным объектом (smart reference)



**Достоинства**
- Позволяет контролировать сервисный объект незаметно для клиента.
- Может работать, даже если сервисный объект ещё не создан.
- Может контролировать жизненный цикл служебного объекта.
**Недостатки**
- Усложняет код программы из-за введения дополнительных классов.
- Увеличивает время отклика от сервиса.