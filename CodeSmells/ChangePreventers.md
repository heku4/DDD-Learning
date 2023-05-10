### Change Preventers (Divergent Change, Shotgun Surgery, Parallel Inheritance Hierarchies)

[how-to-solve-parallel-inheritance-hierarchies-when-we-try-to-reuse-code-through](https://stackoverflow.com/questions/71242936/how-to-solve-parallel-inheritance-hierarchies-when-we-try-to-reuse-code-through)

- #### Divergent Change

Divergent Change is when adding a simple feature makes the developer change many unrelated methods inside a class.

**For example:**

```csharp
public CommunicationFailureValidator
{
    public async Task<bool> ValidateMuAsync(
            MonitoringUnit mu,
            DateTime dateOfImportStart,
            DateTime dateOfImportEnd)
    {
        // validate
        return isValid;
    }

    public async Task<bool> ValidateCtrlAsync(
            MonitoringUnit mu,
            Controller ctrl,
            DateTime dateOfImportStart,
            DateTime dateOfImportEnd)
    {
        // validate
        return isValid;
    }
}
```

The validation process is same in the both methods of the CommunicationFailureValidator, but for logging and another secondary functions we separate this process on two methods.

**How to fix:**

```csharp
public class Device;
{
    //...
}

public class MonitoringUnit: Device
{
    //...
}

public class Controller: Device
{
    //...
}

// extract class
public class CommunicationDeviceValidator
{
    private bool ValidateDeviceAsync(
            Device device,
            DateTime dateOfImportStart,
            DateTime dateOfImportEnd)
    {
        // validate
        return isValid;
    }
}

public class CommunicationFailureValidator
{
    public async Task<bool> ValidateMuAsync(
            MonitoringUnit mu,
            DateTime dateOfImportStart,
            DateTime dateOfImportEnd)
    {
        var validator = new CommunicationDeviceValidator();
        var isMuValid = validator.ValidateDeviceAsync(mu, dateOfImportStart, dateOfImportEnd);
        // ValidateMuAsync unique logic 
        return isMuValid;
    }

    public async Task<bool> ValidateCtrlAsync(
            MonitoringUnit mu,
            Controller ctrl,
            DateTime dateOfImportStart,
            DateTime dateOfImportEnd)
    {
        var validator = new CommunicationDeviceValidator();
        var isCtrlValid = validator.ValidateDeviceAsync(ctrl, dateOfImportStart, dateOfImportEnd);
        // ValidateCtrlAsync unique logic 
        return isCtrlValid;
    }
    
}
```

- #### Shotgun Surgery
Shotgun Surgery refers to when a single change is made to multiple classes simultaneously.

**For example:**

```csharp

public class PgDeviceOperations
{
    public DBConnection Connect(string connectionString)
    {
        // connect
    } 

} 

public class PgLocationOperations
{
    public DBConnection Connect(string connectionString)
    {
        // connect
    } 
}

```

Connection logic changed (add **using** or **logging** )

```csharp

public class PgDeviceOperations
{
    public DBConnection Connect(string connectionString)
    {
        // connect
        using ...

        _logger.Log();
    } 

} 

public class PgLocationOperations
{
    public DBConnection Connect(string connectionString)
    {
        // connect
        using ...

        _logger.Log();
    }
}

```

**How to fix:**

Change DB or connection logic

```csharp

public class DbConnectionCreator()
{
    public DBConnection Connect(string connectionString)
    {
        // connect
        using ...

        _logger.Log();
    } 

} 

public class PgDeviceOperations
{
    private DbConnectionCreator _connectionCreator;

    public PgDeviceOperations(DbConnectionCreator connectionCreator)
    {
        _connectionCreator = connectionCreator;
    }


    public DBConnection Connect(string connectionString)
    {
        return _connectionCreator.Connect();
    } 

} 

public class PgLocationOperations
{
    private DbConnectionCreator _connectionCreator;

    public PgLocationOperations(DbConnectionCreator connectionCreator)
    {
        _connectionCreator = connectionCreator;
    }

    public DBConnection Connect(string connectionString)
    {
        return _connectionCreator.Connect();
    } 
}

```


**OR** merge into one class :

```csharp

public class PgDeviceReadOperations
{
    public DBConnection Connect(string connectionString)
    {
        // connect
        using ...

        _logger.Log();
    } 

} 

public class PgDeviceWriteOperations
{
    public DBConnection Connect(string connectionString)
    {
        // connect
        using ...

        _logger.Log();
    }
}

public class PgDeviceReadOperations
{
    public DBConnection Connect(string connectionString)
    {
        // connect
        using ...

        _logger.Log();
    }
}

```

**How to fix:**

```csharp

public class PgDeviceOperations
{
    public DBConnection Connect(string connectionString)
    {
        // connect
        using ...

        _logger.Log();
    }

    public Device CreateDevice()
    {
        // ...
    }

    public Device GetDevice()
    {
        // ...
    }
} 
```

- #### Parallel Inheritance Hierarchies

Whenever you create a subclass for a class, you find yourself needing to create a subclass for another class.

**For example:**

```csharp

public abstract class Sportsman
{
    public string Name { get; set; }

    public abstract string ShowGoal();
}

public class Runner : Sportsman
{
    public override string ShowGoal()
    {
        return new RunnerGoal().Get();
    }
}

public abstract class Goal
{
    public abstract string Get();
}


public class RunnerGoal : Goal
{
    public override string Get()
    {
        return "Run 1 km";
    }
}
```

**How to fix:**


```csharp

public interface IGoal
{
    string Visit(Sportsman sportsman);
}

public class FootballerGoal : IGoal
{
    public string Visit(Sportsman sportsman)
    {
        return "Score 1 goal";
    }
}

public class Footballer : Sportsman
{
    public string ShowGoal(IGoal goal) 
    {
        return goal.Visit(this);
    }
}

new Footballer().GetGoal(new FootballerGoal())
```

**OR** Collapse a hierarchy (It can break SRP) :

```csharp

public class Footballer : Sportsman
{
    public override string ShowGoal()
    {
        return new FootballerGoal().Get();
    }
    
    public string GetGoal()
    {
        return "Score 1 goal";
    }
}

```