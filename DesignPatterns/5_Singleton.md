## Singleton

Singleton - порождающий паттерн, который гарантирует, что для определенного класса будет создан только один объект, а также предоставит к этому объекту точку доступа.

```csharp
class Singleton
{
    private static Singleton instance;
 
    private Singleton()
    {}
 
    public static Singleton GetInstance()
    {
        if (instance == null)
            instance = new Singleton();
        return instance;
    }
}
```
Для C# и .NET возможна проблема с синглтоном, вызванным в разных потоках


```csharp
class Singleton
{
    private static Singleton instance;
    private static object syncRoot = new Object();
 
    private Singleton()
    {}
 
    public static Singleton GetInstance()
    {
        if (instance == null)
        {
            lock (syncRoot)
            {
                if (instance == null)
                    instance = new Singleton();
            }
        }

        return instance;
    }
}
```

Еще одна потокобезопасная реализация

```csharp
public class Singleton
{
    private static readonly Singleton instance = new Singleton();
 
    public string Date { get; private set; }
 
    private Singleton()
    {
        Date = System.DateTime.Now.TimeOfDay.ToString();
    }
 
    public static Singleton GetInstance()
    {
        return instance;
    }
}
```
**Преимущества**
- Гарантирует наличие единственного экземпляра класса.
- Предоставляет к нему глобальную точку доступа.
- Реализует отложенную инициализацию объекта-одиночки.

**Недостатки**
- Нарушает принцип единственной ответственности класса?
- Маскирует плохой дизайн.
- Проблемы мультипоточности.
- Требует постоянного создания Mock-объектов при юниттестировании