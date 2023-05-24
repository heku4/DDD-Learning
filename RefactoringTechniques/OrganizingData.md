## Organizing Data

- #### Change Value to Reference
Эта техника помогает решить проблему существования одинаковых экзепляров класса, которые на самом деле представляют один объект
**Before**

```csharp
public class Customer
{
  public string Name
  {
    get;
    set;
  }

    // TODO:Replace Constructor with Factory Method.
  public Customer(string name)
  {
    this.Name = name;
  }
}

public class Order
{
  // ...
    private Customer customer;

    public string CustomerName
    {
        get { return customer.Name; }
        set { customer.Name = value; }
    }

    public Order(string customerName)
    {
        this.customer = new Customer(customerName);
    }
}

// Client code, which uses Order class.
private static int NumberOfOrdersFor(List<Order> orders, string customer)
{
    int result = 0;

    if (orders != null)
    {
        foreach (Order order in orders)
        {
            if (string.Equals(order.CustomerName, customer))
            {
                result++;
            }
        }
    }

    return result;
}
```

**After**

```csharp
public class Customer
{
    // 1.
    private static Hashtable instances = new Hashtable();

    public string Name
    {
        get;
        private set;
    }

    private Customer(string name)
    {
        this.Name = name;
    }

    public static Customer GetByName(string name)
    {
        return (Customer) instances[name];
    }

    //TODO: This code should be executed at the program launch.
    //This code can be changed to a Factory Method (with private constructor)
    // 2.
    public static void LoadCustomers()
    {
        new Customer("Lemon Car Hire").Store();
        new Customer("Associated Coffee Machines").Store();
        new Customer("Bilston Gasworks").Store();
    }
    private void Store()
    {
        instances.Add(this.Name, this);
    }
}

public class Order
{
  // ...
  private Customer customer;

  public string CustomerName
  {
    get { return customer.Name; }
  }

  public Order(string customerName)
  {
    SetCustomer(customerName);
  }

  public SetCustomer(string customerName)
  {
    // 3.
    customer = Customer.GetByName(customerName);
  }
}
//…
// Client code, which uses Order class.
private static int NumberOfOrdersFor(List<Order> orders, string customer)
{
  int result = 0;

  if (orders != null)
  {
    foreach (Order order in orders)
    {
        // 4.
        if (string.Equals(order.CustomerName, customer))
        {
            result++;
        }
    }
  }

  return result;
}
```


- #### Change Reference to Value
Для обеспечения неизменяемости объекта, он не должен иметь публичных сеттеров или других метожов меняющих его состояние. Присвоение значений полям объекта может быть только в конструкторе.

**Before**

```csharp
public class Customer
    {
    private static Hashtable instances = new Hashtable();

    public string Name
    {
        get;
        private set;
    }

    public DateTime BirthDate
    {
        get;
        set;
    }

    private Customer(string Name)
    {
        this.Name = Name;
    }

    public static Customer Get(string name)
    {
        Customer result = (Customer)instances[name];

        if (result == null)
        {
            result = new Customer(name);
            instances.Add(name, result);
        }

        return result;
    }
}

    // Somewhere in client code
    Customer john = Customer.Get("John Smith");
    john.BirthDate = new DateTime(1985, 1, 1);
```

**After**

```csharp
public class Customer
{
    public string Name
    {
        get;
        private set;
    }
    public DateTime BirthDate
    {
        get;
        // 1. to private
        private set;
    }

    // 2. use DateTime in constructor
    public Customer(string Name, DateTime BirthDate)
    {
        this.Name = Name;
        this.BirthDate = BirthDate;
    }

    // 3.1 value objects must be equal if name and date are equal
    public override bool Equals(Object obj)
    {
        Customer other = obj as Customer;

        if (other == null)
        return false;

        return this.BirthDate == other.BirthDate && string.Equals(this.Name, other.Name, StringComparison.Ordinal);
    }

    //3.2 in case of the C# lanuage
    public override int GetHashCode()
    {
        int hashCode = 11;
        unchecked
        {
        if (Name != null)
            hashCode = hashCode * 22 + Name.GetHashCode();
        hashCode = hashCode * 22 + BirthDate.GetHashCode();
        }
        return hashCode;
    }
}

// Somewhere in client code
Customer john = new Customer("John Smith", new DateTime(1985, 1, 1));
```


- #### Duplicate Observed Data
На примере GUI становится ясно, что код, обрабатывающий интерфейс пользователя, отделен от кода, обрабатывающего бизнес-логику. Если бизнес-логика встроена в интерфейс пользователя, необходимо разделить поведение на части (в основном с помощью декомпозиции и перемещения методов). Но данные нельзя перемещать, их надо скопировать и обеспечить механизм синхронизации.
**Before**
![duplicateObservedData](duplicateObservedData.png)
```csharp
public partial class IntervalWindow : Form
{
    public IntervalWindow()
    {
        InitializeComponent();
    }

    private void CalculateLength()
    {
        int start = int.Parse(tbStart.Text);
        int end = int.Parse(tbEnd.Text);
        int length = end - start;
        tbLength.Text = length.ToString();
    }
    private void CalculateEnd()
    {
        int start = int.Parse(tbStart.Text);
        int length = int.Parse(tbLength.Text);
        int end = start + length;
        tbEnd.Text = end.ToString();
    }

    private void OnTextBoxLeave(object sender, EventArgs e)
    {
        TextBox tb = sender as TextBox;
        
        if (tb != null)
        {
            int tmp;
            if (!int.TryParse(tb.Text, out tmp))
            {
                tb.Text = "0";
            }
            
            if (tb == tbStart)
            {
                CalculateLength();
            }
            else if (tb == tbEnd)
            {
                CalculateLength();
            }  
            else if (tb == tbLength)
            {
                CalculateEnd();
            }
        }
    }
}
```
Необходимо выделение всех перерасчётов длины и конечного значения в отдельный класс предметной области. 
**After**

```csharp
// 3.1 mplement the Observer pattern. IObserver<Interval>
public partial class IntervalWindow : Form, IObserver<Interval>
{
    // 2. Create reference to Interval class
    private Interval subject;

    // set value if it changed in the observable class
    private string Start
    {
        get{ return subject.Start; }
        set{ subject.Start = value; }
    }
    private string End
    {
        get{ return subject.End; }
        set{ subject.End = value; }
    }
    private string Length
    {
        get{ return subject.Length; }
        set{ subject.Length = value; }
    }

    // 5.1 In constructor create interval instance and subscribe on
    public IntervalWindow()
    {
        InitializeComponent();

        subject = new Interval();
        subject.Subscribe(this);
        OnNext(subject);
    }

    // 4.1 Interface implementation
    public void OnNext(Interval interval)
    {
        tbStart.Text = interval.Start;
        tbEnd.Text = interval.End;
        tbLength.Text = interval.Length;
    }

    // No implementation needed: Method is not called by the Interval class.
    public void OnError(Exception e)
    {
        // No implementation.
    }

    // No implementation needed: Method is not called by the Interval class.
    public void OnCompleted()
    {
        // No implementation.
    }

    private void OnTextBoxLeave(object sender, EventArgs e)
    {
        // same logic as before
        TextBox tb = sender as TextBox;
        
        if (tb != null)
        {
            int tmp;
            if (!int.TryParse(tb.Text, out tmp))
            {
                tb.Text = "0";
            }
            
            if (tb == tbStart)
            {
                this.Start = tb.Text;
                subject.CalculateLength();
            }
            else if (tb == tbEnd)
            {
                this.End = tb.Text;
                subject.CalculateLength();
            }  
            else if (tb == tbLength)
            {
                this.Length = tb.Text;
                subject.CalculateEnd();
            }
        }
    }
}

// 1. Create separated domain class
// 3.1 mplement the Observer pattern. IObservable<Interval>
public class Interval: IObservable<Interval>
{
    // 4.3 observers (subscribers) storing
    private List<IObserver<Interval>> observers;
    private string  _start = "0",
                    _end = "0",
                    _length = "0";

    public string Start
    {
        get{ return _start; }
        // 6.2 when private field is changed this value is sent to subscribers
        set{ OnValueChanged(ref _start, value); }
    }
    public string End
    {
        get{ return _end; }
        set{ OnValueChanged(ref _end, value); }
    }
    public string Length
    {
        get{ return _length; }
        set{ OnValueChanged(ref _length, value); }
    }

    public Interval()
    {
        // 4.4 
        observers = new List<IObserver<Interval>>();
    }

    private void OnValueChanged(ref string oldValue, string newValue)
    {
        if (!string.Equals(oldValue, newValue, StringComparison.Ordinal))
        {
        oldValue = newValue;
        foreach (var observer in observers)
            observer.OnNext(this);
        }
    }

    // 4.2 Interface implementation
    public IDisposable Subscribe(IObserver<Interval> observer)
    {
        if (!observers.Contains(observer))
        {
            observers.Add(observer);
            observer.OnNext(this);
        }

        // For disposable subscribers
        return null;
    }

    // 6.1 now these methods calculate something and store it in private fields
    public void CalculateLength()
    {
        int start = int.Parse(this.Start);
        int end = int.Parse(this.End);
        int length = end - start;
        this.Length = length.ToString();
    }

    public void CalculateEnd()
    {
        int start = int.Parse(this.Start);
        int length = int.Parse(this.Length);
        int end = start + length;
        this.End = end.ToString();
    }
}
```


- #### Self Encapsulate Field
Если при доступе к полю родительского класса необходимо заменить обращение к переменной вычислением значения в подклассе, тогда можно использовать эту технику.
Самоинкапсуляция заключается в реализации доступа к полям через свойства, в том числе, в методах самого класса.

**Before**

```csharp
public class IntRange
{
    private int _low, _high;

    public IntRange(int low, int high)
    {
        _low = low;
        _high = high;
    }

    public bool Includes(int arg)
    {
        return arg >= _low && arg <= _high;
    }

    public void Scale(int factor)
    {
        _high = _high * factor;
    }
}
```

**After**

```csharp
public class IntRange
{
    // 1. reate properties that encapsulate our fields 
    private int Low
    {
        get;
        set;
    }
    private int High
    {
        get;
        set;
    }

    public IntRange(int low, int high)
    {
        // if setter logic is simple we can use it in constructor
        this.Low = low;
        this.High = high;
    }

    public bool Includes(int arg)
    {
        // 2. replace all references to fields with references to the corresponding properties
        return arg >= Low && arg <= High;
    }

    public void Scale(int factor)
    {
        // 2.
        High = High * factor;
    }
}
```


- #### Replace Data Value with Object

**Before**

```csharp

```

**After**

```csharp

```


- #### Replace Array with Object

**Before**

```csharp

```

**After**

```csharp

```


- #### Change Unidirectional Association to Bidirectional

**Before**

```csharp

```

**After**

```csharp

```


- #### Change Bidirectional Association to Unidirectional

**Before**

```csharp

```

**After**

```csharp

```


- #### Encapsulate Field

**Before**

```csharp

```

**After**

```csharp

```


- #### Encapsulate Collection

**Before**

```csharp

```

**After**

```csharp

```


- #### Replace Magic Number with Symbolic Constant

**Before**

```csharp

```

**After**

```csharp

```


- #### Replace Type Code with Class

**Before**

```csharp

```

**After**

```csharp

```


- #### Replace Type Code with Subclasses

**Before**

```csharp

```

**After**

```csharp

```


- #### Replace Type Code with State/Strategy

**Before**

```csharp

```

**After**

```csharp

```


- #### Replace Subclass with Fields

**Before**

```csharp

```

**After**

```csharp

```