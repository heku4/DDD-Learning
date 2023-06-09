## Chain of Responsibility

Chain of Responsibility - поведенческий шаблон проектирования, который позволяет избежать жесткой привязки отправителя запроса к получателю. Все возможные обработчики запроса образуют цепочку, а сам запрос перемещается по этой цепочке. Каждый объект в этой цепочке при получении запроса выбирает, либо закончить обработку запроса, либо передать запрос на обработку следующему по цепочке объекту.

- Handler: определяет интерфейс для обработки запроса. Также может определять ссылку на следующий обработчик запроса

- ConcreteHandler1 и ConcreteHandler2: конкретные обработчики, которые реализуют функционал для обработки запроса. При невозможности обработки и наличия ссылки на следующий обработчик, передают запрос этому обработчику

- Client: отправляет запрос объекту Handler

```csharp

// you can set own priority of money transfer systems

class Program
{
    static void Main(string[] args)
    {
        var receiver = new Receiver(false, true, false);
         
        var bankPaymentHandler = new BankPaymentHandler();
        var moneyPaymentHandler = new MoneyPaymentHandler();
        var paypalPaymentHandler = new PayPalPaymentHandler();
        bankPaymentHandler.Successor = paypalPaymentHandler;
        paypalPaymentHandler.Successor = moneyPaymentHandler;
 
        bankPaymentHandler.Handle(receiver);
 
        Console.Read();
    }
}
 
class Receiver // helper class
{
    public bool UseBankTransfer { get; set; }
    public bool UseMoneyTransfer { get; set; }
    public bool UsePayPalTransfer { get; set; }
    public Receiver(bool bt, bool mt, bool ppt)
    {
        UseBankTransfer = bt;
        UseMoneyTransfer = mt;
        UsePayPalTransfer = ppt;
    }
}

abstract class PaymentHandler // Handler
{
    public PaymentHandler Successor { get; set; }
    public abstract void Handle(Receiver receiver);
}
 
class BankPaymentHandler : PaymentHandler //ConcreteHandler1
{
    public override void Handle(Receiver receiver)
    {
        if (receiver.UseBankTransfer)
            Console.WriteLine("Bank monet transferring");
        else if (Successor != null)
            Successor.Handle(receiver);
    }
}
 
class PayPalPaymentHandler : PaymentHandler // ConcreteHandler2
{
    public override void Handle(Receiver receiver)
    {
        if (receiver.UsePayPalTransfer)
            Console.WriteLine("PayPal  money transferring");
        else if (Successor != null)
            Successor.Handle(receiver);
    }
}

class MoneyPaymentHandler : PaymentHandler // ConcreteHandler3
{
    public override void Handle(Receiver receiver)
    {
        if (receiver.UseMoneyTransfer)
            Console.WriteLine("External system money transferring");
        else if (Successor != null)
            Successor.Handle(receiver);
    }
}
```

**Преимущества**
- Уменьшает зависимость между клиентом и обработчиками.
- Реализует принцип единственной обязанности.
- Реализует принцип открытости/закрытости.

**Недостатки**
- Запрос может остаться никем не обработанным