### Change Preventers (Divergent Change, Shotgun Surgery, Parallel Inheritance Hierarchies)
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
public class Device(){};
{
    //...
}

public class MonitoringUnit(): Device
{
    //...
}

public class Controller(): Device
{
    //...
}

public CommunicationFailureValidator
{
    public async Task<bool> ValidateMuAsync(
            MonitoringUnit mu,
            DateTime dateOfImportStart,
            DateTime dateOfImportEnd)
    {
        var isMuValid = ValidateDeviceAsync(mu, dateOfImportStart, dateOfImportEnd);
        // ValidateMuAsync unique logic 
        return isMuValid;
    }

    public async Task<bool> ValidateCtrlAsync(
            MonitoringUnit mu,
            Controller ctrl,
            DateTime dateOfImportStart,
            DateTime dateOfImportEnd)
    {
        var isCtrlValid = ValidateDeviceAsync(ctrl, dateOfImportStart, dateOfImportEnd);
        // ValidateCtrlAsync unique logic 
        return isCtrlValid;
    }

    private bool ValidateDeviceAsync(
            Device device,
            DateTime dateOfImportStart,
            DateTime dateOfImportEnd)
    {
        // validate
        return isValid;
    }
}
```

- #### Shotgun Surgery
Shotgun Surgery refers to when a single change is made to multiple classes simultaneously.

**For example:**

```csharp
```

**How to fix:**


```csharp
```

- #### Parallel Inheritance Hierarchies

**For example:**

```csharp
```

**How to fix:**


```csharp
```
