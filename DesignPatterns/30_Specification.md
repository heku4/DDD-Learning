## Specification

Specification pattern is a pattern that allows us to encapsulate some piece of domain knowledge into a single unit - specification - and reuse it in different parts of the code base.

```csharp
class Program
{
    static void Main(string[] args)
    {
        var monitors = new List<ComputerMonitor>
        {
            new ComputerMonitor { Name = "Samsung S345", Screen = Screen.CurvedScreen, Type = MonitorType.OLED },
            new ComputerMonitor { Name = "LG L888", Screen = Screen.WideScreen, Type = MonitorType.LED },
            new ComputerMonitor { Name = "Dell D2J47", Screen = Screen.CurvedScreen, Type = MonitorType.LCD }
        };

        var filter = new MonitorFilter();

        var lcdMonitors = filter.Filter(monitors, new MonitorTypeSpecification(MonitorType.LCD));

        foreach (var monitor in lcdMonitors)
        {
            Console.WriteLine($"Name: {monitor.Name}, Type: {monitor.Type}, Screen: {monitor.Screen}");
        }

        var wideScreenMonitors = filter.Filter(monitors, new ScreenSpecification(Screen.WideScreen));
        foreach (var monitor in wideScreenMonitors)
        {
            Console.WriteLine($"Name: {monitor.Name}, Type: {monitor.Type}, Screen: {monitor.Screen}");
        }
    }
}

public interface IFilter<T>
{
    List<T> Filter(IEnumerable<T> monitors, ISpecification<T> specification);
}

public class MonitorFilter : IFilter<ComputerMonitor>
{
    public List<ComputerMonitor> Filter(IEnumerable<ComputerMonitor> monitors, ISpecification<ComputerMonitor> specification) =>
        monitors.Where(specification.isSatisfied).ToList();
}

public class ComputerMonitor
{
    public string Name { get; set; }
    public MonitorType Type { get; set; }
    public Screen Screen { get; set; }
}
public enum MonitorType
{
    OLED,
    LCD,
    LED
}

public enum Screen
{
    WideScreen,
    CurvedScreen
}

public interface ISpecification<T>
{
    bool isSatisfied(T item);
}

public class ScreenSpecification : ISpecification<ComputerMonitor> // Concrete Specification 1
{
    private readonly Screen _screen;

    public ScreenSpecification(Screen screen)
    {
        _screen = screen;
    }

    public bool isSatisfied(ComputerMonitor item) => item.Screen == _screen;
}

public class MonitorTypeSpecification: ISpecification<ComputerMonitor> // Concrete Specification 2
{
    private readonly MonitorType _type;

    public MonitorTypeSpecification(MonitorType type)
    {
        _type = type;
    }

    public bool isSatisfied(ComputerMonitor item) => item.Type == _type;
}
```