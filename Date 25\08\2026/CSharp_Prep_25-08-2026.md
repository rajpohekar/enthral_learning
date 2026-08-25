# C# Language Preparation

## Topics Covered — 25/08/2026

1. Exception Handling
2. Nullable Types
3. Nullable Reference Types
4. `enum`
5. Structs
6. `readonly`
7. Static Classes
8. Object Initializers
9. Collection Initializers
10. Properties with Getters/Setters
11. Auto-Properties

---

# 1. Exception Handling

Exception handling is used to handle errors that occur during program execution and prevent unexpected termination.

## What is an Exception?

An exception is an error condition that occurs during program execution and disrupts the normal flow of the program.

```csharp
int result = 10 / 0;
```

This causes a `DivideByZeroException`.

Common exceptions:

```text
DivideByZeroException
NullReferenceException
IndexOutOfRangeException
FormatException
InvalidOperationException
```

## `try`

The `try` block contains code that may throw an exception.

```csharp
try
{
    int result = 10 / 0;
}
```

## `catch`

The `catch` block handles an exception thrown from the `try` block.

```csharp
try
{
    int result = 10 / 0;
}
catch (Exception ex)
{
    Console.WriteLine(ex.Message);
}
```

Useful exception information includes:

```csharp
ex.Message
ex.StackTrace
ex.GetType()
```

## `finally`

`finally` executes after the `try`/`catch` processing and is commonly used for cleanup.

```csharp
try
{
    int result = 10 / 2;
}
catch (Exception ex)
{
    Console.WriteLine(ex.Message);
}
finally
{
    Console.WriteLine("Done");
}
```

## `throw`

`throw` is used to explicitly raise an exception.

```csharp
int age = -5;

if (age < 0)
{
    throw new Exception("Age cannot be negative.");
}
```

## Multiple `catch` Blocks

```csharp
try
{
    int number = int.Parse("abc");
}
catch (FormatException)
{
    Console.WriteLine("Invalid number format.");
}
catch (OverflowException)
{
    Console.WriteLine("Number is too large.");
}
catch (Exception)
{
    Console.WriteLine("Some other error occurred.");
}
```

Specific exception types should generally be handled before the general `Exception` type.

## Custom Exceptions

```csharp
class InvalidAgeException : Exception
{
    public InvalidAgeException(string message)
        : base(message)
    {
    }
}
```

Usage:

```csharp
try
{
    int age = -10;

    if (age < 0)
    {
        throw new InvalidAgeException("Age cannot be negative.");
    }
}
catch (InvalidAgeException ex)
{
    Console.WriteLine(ex.Message);
}
```

---

# 2. Nullable Types

A nullable value type can contain either a value or `null`.

```csharp
int? age = null;
```

`int?` is shorthand for:

```csharp
Nullable<int>
```

It can later contain a value:

```csharp
age = 23;
```

Check for a value:

```csharp
if (age.HasValue)
{
    Console.WriteLine(age.Value);
}
```

Nullable types are useful when working with database values that may contain `NULL`.

Example:

```csharp
DateTime? dateOfJoining = null;
```

## Null-Coalescing Operator `??`

```csharp
int? age = null;

int actualAge = age ?? 18;
```

If `age` is `null`, `actualAge` becomes `18`.

---

# 3. Nullable Reference Types

A nullable reference type explicitly indicates that a reference may be `null`.

```csharp
string? name = null;
```

Compare:

```csharp
int? age = null;
string? name = null;
```

```text
int?
→ nullable value type

string?
→ nullable reference type
```

Basic awareness is sufficient for now.

---

# 4. `enum`

An `enum` represents a fixed set of named values.

```csharp
enum EmployeeType
{
    Permanent,
    Contract,
    Intern
}
```

Usage:

```csharp
EmployeeType type = EmployeeType.Permanent;
```

By default:

```text
Permanent → 0
Contract  → 1
Intern    → 2
```

Values can also be explicitly assigned:

```csharp
enum EmployeeType
{
    Permanent = 1,
    Contract = 2,
    Intern = 3
}
```

Enums are useful when a value must come from a predefined set of choices.

---

# 5. Structs

A `struct` is a value type that can contain fields, properties, methods, and constructors.

```csharp
struct Point
{
    public int X;
    public int Y;
}
```

Usage:

```csharp
Point point = new Point();

point.X = 10;
point.Y = 20;
```

Important distinction:

```text
class
→ reference type

struct
→ value type
```

---

# 6. `readonly`

A `readonly` field can be assigned during declaration or in a constructor, but cannot normally be changed afterward.

```csharp
class Employee
{
    public readonly int Id;

    public Employee(int id)
    {
        Id = id;
    }
}
```

```csharp
Employee emp = new Employee(101);

// emp.Id = 102; // Error
```

Think:

```text
readonly
→ initialize
→ then don't modify
```

---

# 7. Static Classes

A `static` class cannot be instantiated.

```csharp
static class Calculator
{
    public static int Add(int a, int b)
    {
        return a + b;
    }
}
```

Call it using the class name:

```csharp
int result = Calculator.Add(10, 20);
```

A familiar .NET example is:

```csharp
Math.Abs(-10);
```

---

# 8. Object Initializers

An object initializer allows an object to be created and its properties assigned in one statement.

Without initializer:

```csharp
Employee emp = new Employee();

emp.Id = 101;
emp.Name = "Raj";
emp.Salary = 50000;
```

With initializer:

```csharp
Employee emp = new Employee
{
    Id = 101,
    Name = "Raj",
    Salary = 50000
};
```

---

# 9. Collection Initializers

A collection can be initialized directly.

```csharp
List<string> names = new List<string>
{
    "Raj",
    "Amit",
    "Rahul"
};
```

Dictionary example:

```csharp
Dictionary<int, string> employees =
    new Dictionary<int, string>
    {
        { 101, "Raj" },
        { 102, "Amit" },
        { 103, "Rahul" }
    };
```

---

# 10. Properties with Getters and Setters

A property provides controlled access to class data.

```csharp
public string Name { get; set; }
```

```text
get → read
set → assign/change
```

Explicit property:

```csharp
private int age;

public int Age
{
    get
    {
        return age;
    }

    set
    {
        if (value >= 0)
        {
            age = value;
        }
    }
}
```

Inside a setter, `value` represents the value being assigned.

## Read-Only Property

```csharp
public string Company
{
    get
    {
        return "Enthral";
    }
}
```

## Private Setter

```csharp
public int Id { get; private set; }
```

The property can be read publicly, but only the class can set it.

---

# 11. Auto-Properties

An auto-implemented property uses the short syntax:

```csharp
public string Name { get; set; }
```

Instead of explicitly defining a backing field:

```csharp
private string name;

public string Name
{
    get
    {
        return name;
    }

    set
    {
        name = value;
    }
}
```

C# automatically provides the required backing storage for an auto-property.

Example:

```csharp
class Employee
{
    public int EmpId { get; set; }

    public string EmpName { get; set; }

    public string EmpType { get; set; }

    public DateTime? DateOfJoining { get; set; }

    public bool IsDeleted { get; set; }
}
```

---

# Combined Example

```csharp
enum EmployeeType
{
    Permanent,
    Contract,
    Intern
}

class Employee
{
    public int Id { get; set; }

    public string Name { get; set; }

    public EmployeeType Type { get; set; }

    public DateTime? DateOfJoining { get; set; }

    public readonly string Company;

    public Employee(
        int id,
        string name,
        EmployeeType type,
        string company)
    {
        Id = id;
        Name = name;
        Type = type;
        Company = company;
    }
}
```

Creating an object:

```csharp
Employee emp = new Employee(
    101,
    "Raj",
    EmployeeType.Permanent,
    "Enthral"
)
{
    DateOfJoining = DateTime.Now
};
```

Concepts used:

```text
Employee
→ class

Id, Name, Type
→ auto-properties

EmployeeType
→ enum

DateTime?
→ nullable value type

Company
→ readonly field

new Employee(...)
→ constructor

{ DateOfJoining = ... }
→ object initializer
```

---

# Quick Revision Checklist — 25/08/2026

## Exception Handling

- [ ] What is an exception?
- [ ] `try`
- [ ] `catch`
- [ ] `finally`
- [ ] `throw`
- [ ] Multiple `catch` blocks
- [ ] Custom exceptions — basic understanding

## Important C# Features

- [ ] Nullable value types
- [ ] Nullable reference types
- [ ] `enum`
- [ ] Structs
- [ ] `readonly`
- [ ] Static classes
- [ ] Object initializers
- [ ] Collection initializers
- [ ] Properties with getters/setters
- [ ] Auto-properties

---

# Overall C# Progress

```text
Level 5 → Methods              ✅
Level 6 → Arrays & Collections ✅
Level 7 → OOP                  ✅
Level 8 → Exception Handling   ✅
Level 9 → Important Features   ✅
```

**Date: 25/08/2026**
