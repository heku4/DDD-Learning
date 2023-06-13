## Object Pool

Object Pool - Object pools can improve application performance in situations where you require multiple instances of a class and the class is expensive to create or destroy. When a client program requests a new object, the object pool first attempts to provide one that has already been created and returned to the pool. If none is available, only then is a new object created.

```csharp
using System.Collections.Concurrent;

namespace ObjectPoolExample
{
    public class ObjectPool<T>
    {
        private readonly ConcurrentBag<T> _objects;
        private readonly Func<T> _objectGenerator;

        public ObjectPool(Func<T> objectGenerator)
        {
            _objectGenerator = objectGenerator;
            _objects = new ConcurrentBag<T>();
        }

        public T Get()
        {
            if(_objects.TryTake(out T item))
            {
                return item;
            } 
            
            return _objectGenerator();
        } 


        public void Return(T item)
        {
            _objects.Add(item);   
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            using var cts = new CancellationTokenSource();

            // Create an opportunity for the user to cancel.
            _ = Task.Run(() =>
            {
                if (char.ToUpperInvariant(Console.ReadKey().KeyChar) == 'C')
                {
                    cts.Cancel();
                }
            });

            var pool = new ObjectPool<ExampleObject>(() => new ExampleObject());

            Parallel.For(0, 1000000, (i, loopState) =>
            {
                var example = pool.Get();
                try
                {
                    Console.CursorLeft = 0;
                    // This is the bottleneck in our application. All threads in this loop
                    // must serialize their access to the static Console class.
                    Console.WriteLine($"{example.GetValue(i):####.####}");
                }
                finally
                {
                    pool.Return(example);
                }

                if (cts.Token.IsCancellationRequested)
                {
                    loopState.Stop();
                }
            });

            Console.WriteLine("Press the Enter key to exit.");
            Console.ReadLine();
        }
    }

    // A toy class that requires some resources to create.
    class ExampleObject
    {
        public int[] Nums { get; set; }

        public ExampleObject()
        {
            Nums = new int[1000000];
            var rand = new Random();
            for (int i = 0; i < Nums.Length; i++)
            {
                Nums[i] = rand.Next();
            }
        }

        public double GetValue(long i) => Math.Sqrt(Nums[i]);
    }
}
```