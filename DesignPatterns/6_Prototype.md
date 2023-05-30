## Prototype

Прототип — это порождающий паттерн проектирования,который позволяет копировать объекты, не вдаваясь в подробности их реализации. Состоит из:
- Интерфейс прототипов описывает операции клонирования. В большинстве случаев — это единственный метод clone
- Конкретный прототип реализует операцию клонирования самого себя. Инкапсулирует эту операцию от клиента

Prototype design pattern is used when the Object creation is a costly affair and requires a lot of time and resources and you have a similar object already existing. Prototype pattern provides a mechanism to copy the original object to a new object and then modify it according to our needs.

```csharp
// prototype interface
interface IFigure
{
    IFigure Clone();
    void GetInfo();
}
 
class Rectangle: IFigure
{
    int _width;
    int _height;
    public Rectangle(int w, int h)
    {
        _width = w;
        _height = h;
    }
 
    public IFigure Clone()
    {
        return new Rectangle(this._width, this._height);
    }
    public void GetInfo()
    {
        Console.WriteLine("Прямоугольник длиной {0} и шириной {1}", _height, _width);
    }
}
 
class Circle : IFigure
{
    int _radius;
    public Circle(int r)
    {
        _radius = r;
    }
 
    public IFigure Clone()
    {
        return new Circle(this._radius);
    }
    public void GetInfo()
    {
        Console.WriteLine("Круг радиусом {0}", _radius);
    }
}


class Program
{
    static void Main(string[] args)
    {
        IFigure figure = new Rectangle(30,40);
        IFigure clonedFigure = figure.Clone();
        figure.GetInfo();
        clonedFigure.GetInfo();
 
        figure = new Circle(30);
        clonedFigure=figure.Clone();
        figure.GetInfo();
        clonedFigure.GetInfo();
    }
}

```

**Преимущества**
- Позволяет клонировать объекты, не привязываясь к их конкретным классам.
- Меньше повторяющегося кода инициализации объектов.
- Ускоряет создание объектов.
- Альтернатива созданию подклассов для конструирования сложных объектов.

**Недостатки**
- Сложно клонировать составные объекты, имеющие ссылки на другие объекты.