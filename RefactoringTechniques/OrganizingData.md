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
На начальных этапах разработки простые данные могут быть использованые в виде простых элементов. Например, телефон - строка. Позднее выяснится, что телефон может иметь код зоны или особое форматирование. В таких случаях необходимо преобразовать данные в объект.

**Before**

```csharp
public class Order
{
  // ...
  private string customer;

  public string Customer
  {
    get { return customer; }
    set { customer = value; }
  }

  public Order(string customer)
  {
    this.Customer = customer;
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
            if (string.Equals(order.Customer, customer))
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
public class Order
{
    // 2. change the type of the customer field and change the related methods and properties 
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

// 1. create a Customer class and move the other customer data and behaviors to this class
public class Customer
{
    public string Name
    {
        get;
        set;
    }

    public Customer(string name)
    {
        this.Name = name;
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
            // 3. use updated properties
            if (string.Equals(order.CustomerName, customer))
            {
                result++;
            }
        }
    }

  return result;
}
```


- #### Replace Array with Object
Tckb ceotcndetn массив, некоторые элементы которого могут означать разные сущности.

**Before**

```csharp
public class Tournament
{
    string[] row = new string[2];

    public Tournament()
    {
        row[0] = "Liverpool";
        row[1] = "15";
    }
    public void DisplayScore()
    {
        string name = row[0];
        int score = Convert.ToInt32(row[1]);
        // ...
    }
}
```

**After**

```csharp
public class Tournament
{
    // 2. Use the Perfomance class instead of array
    Performance row = new Performance();

    public Tournament()
    {
        row.CommandName = "Liverpool";
        row.Score = 15;
    }
    public void DisplayScore()
    {
        string name = row.CommandName;
        int score = row.Score;
        // ...
    }
}

// 1. Create class perfomance
public class Performance
{
    public string CommandName
    {
        get;
        set;
    }
    public int Score
    {
        get;
        set;
    }
}
```


- #### Change Unidirectional Association to Bidirectional
Метод поможет решить проблему, когда есть два класса, каждый из которых должен использовать функции другого, но ссылка между ними есть только в одном направлении.

**Before**

```csharp
public class Order
{
  // ...
  private Customer customer;

  public Customer Customer
  {
    get { return customer; }
    set { customer = value; }
  }
}

public class Customer
{
  // ...
}
```

**After**
Чтобы выбрать какой из классов будет отвечать за управление связью:
- Если оба объекта представляют собой объекты ссылок, и связь имеет тип «один-ко-многим», то управляющим будет объект, содержащий одну ссылку. (То есть если у одного клиента несколько заказов, связью управляет заказ.)
- Если один объект является компонентом другого (т. е. связь имеет тип «целое-часть»), управлять связью должен составной объект.
- Если оба объекта представляют собой объекты ссылок, и связь имеет тип «многие-ко-многим», то в качестве управляющего можно произвольно выбрать класс заказа или класс клиента.

```csharp
public class Order
{
  // ...
    private Customer customer;

    public Customer Customer
    {
        get { return customer; }
        set {
            // first direction
            // Remove order from old customer.
            if (customer != null)
            {
                customer.Orders.Remove(this);
            }

            customer = value;

            // Add order to new customer.
            if (customer != null)
            {
                customer.Orders.Add(this);
            }
        }
    }
}

public class Customer
{
    // 1. any customer can have multiple orders
    private HashSet<Order> orders = new HashSet<Order>();

    // Should be used in Order class only.
    public HashSet<Order> Orders
    {
        get { return orders; }
    }

    public void AddOrder(Order order)
    {
        // second direction
        order.Customer = this;
    }
}
```


- #### Change Bidirectional Association to Unidirectional
Двунаправленные связи удобны, но создают помехи в виде дополнительной сложности поддержки двусторонних ссылок и обеспечения корректности создания и удаления объектов. Сначала неободимо решить какой класс будет управляющим ("dominant").
**Before**

```csharp
public class Order
{
  // ...
  private Customer customer;

  public Customer Customer
  {
    get {
      return customer;
    }
    set {
      // Remove order from old customer.
      if (customer != null)
      {
        customer.Orders.Remove(this);
      }
      customer = value;
      // Add order to new customer.
      if (customer != null)
      {
        customer.Orders.Add(this);
      }
    }
  }

  public double GetDiscountedPrice()
  {
    return GetGrossPrice() * (1 - this.Customer.GetDiscount());
  }
}

public class Customer
{
  // ...
  private HashSet<Order> orders = new HashSet<Order>();

  // Should be used in Order class only.
  public HashSet<Order> Orders
  {
    get {
      return orders;
    }
  }

  public void AddOrder(Order order)
  {
    order.Customer = this;
  }

  public double GetPriceFor(Order order)
  {
     Assert.IsTrue(orders.Contains(order));
     return order.GetDiscountedPrice();
  }
}
```

**After**
Заказы должны появляться, только в том случае, если покупатель уже создан. Это позволяет отказаться от двусторонней связи между классами и избавиться от связи заказа к покупателю.

```csharp
public class Order
{
  // ...
    public Customer Customer
    {
        get {
        foreach (Customer customer in Customer.GetInstances())
        {
            if (customer.ContainsOrder(this))
            return customer;
        }
        return null;
        }
    }

    public double GetDiscountedPrice()
    {
        return GetGrossPrice() * (1 - this.Customer.GetDiscount());
    }
}

public class Customer
{
    // ...
    private HashSet<Order> orders = new HashSet<Order>();

    public void AddOrder(Order order)
    {
        orders.Add(order);
    }

    public double GetPriceFor(Order order)
    {
        Assert.IsTrue(orders.Contains(order));
        return order.GetDiscountedPrice();
    }
}
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