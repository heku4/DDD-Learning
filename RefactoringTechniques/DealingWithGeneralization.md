## Dealing with Generalization

- ### Pull Up Field
Если в двух классах есть одинаковое поле, то их стоит переместить в родительский класс.

- ### Pull Up Method

Если в двух классах есть методы с идентичными результатами, то их стоит переместить в родительский класс.

- ### Pull Up Constructor Body

Если имеются конструкторы подклассов с почти идентичными телами, то стоит вызывать родительский конструктор из подклассов.
**Before:**

```csharp
public class Manager: Employee 
{
    public Manager(string name, string id, int grade) 
    {
        this.name = name;
        this.id = id;
        this.grade = grade;
    }
    // ...
}
```

**After:**

```csharp
public class Manager: Employee 
{
    public Manager(string name, string id, int grade): base(name, id)
    {
        this.grade = grade;
    }
    // ...
}
```

- ### Push Down Field

**Before:**

```csharp

```

**After:**

```csharp

```

- ### Push Down Method

**Before:**

```csharp

```

**After:**

```csharp

```

- ### Extract Subclass

**Before:**

```csharp

```

**After:**

```csharp

```

- ### Extract Superclass

**Before:**

```csharp

```

**After:**

```csharp

```

- ### Extract Interface

**Before:**

```csharp

```

**After:**

```csharp

```

- ### Collapse Hierarchy

**Before:**

```csharp

```

**After:**

```csharp

```

- ### Form Template Method

**Before:**

```csharp

```

**After:**

```csharp

```

- ###  Replace Inheritance with Delegation

**Before:**

```csharp

```

**After:**

```csharp

```

- ### Replace Delegation with Inheritance

**Before:**

```csharp

```

**After:**

```csharp

```
