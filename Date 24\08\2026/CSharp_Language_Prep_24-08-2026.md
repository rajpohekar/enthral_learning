# C# Language Preparation

## Topics Covered — 24/08/2026

1. Methods
2. Arrays
3. Collections
4. Classes and Objects
5. Fields and Properties
6. Constructors
7. `this`
8. Access Modifiers
9. Encapsulation
10. Abstraction
11. Inheritance
12. Polymorphism
13. Method Overloading
14. Method Overriding
15. `virtual` and `override`
16. `abstract`
17. Interfaces
18. Base and Derived Classes
19. `base`
20. `sealed`

---

# 1. Methods

## What is a Method?

A method is a named block of code that performs a specific task.

```csharp
void DisplayName()
{
    Console.WriteLine("Raj Pohekar");
}
```

Calling the method:

```csharp
DisplayName();
```

## Method Declaration

General syntax:

```csharp
returnType MethodName(parameters)
{
    // code
}
```

Example:

```csharp
int Add(int a, int b)
{
    return a + b;
}
```

- `int` → return type
- `Add` → method name
- `int a`, `int b` → parameters
- `return` → sends a value back

## Parameters vs Arguments

```csharp
int Add(int a, int b)
```

`a` and `b` are parameters.

```csharp
Add(10, 20);
```

`10` and `20` are arguments.

## Return Types

Methods can return different types:

```csharp
int GetAge()
{
    return 23;
}

string GetName()
{
    return "Raj";
}

bool IsAdult()
{
    return true;
}
```

## `void`

`void` means the method does not return a value.

```csharp
void DisplayMessage()
{
    Console.WriteLine("Hello");
}
```

`return` can also be used in a `void` method to exit early.

```csharp
void CheckAge(int age)
{
    if (age < 18)
        return;

    Console.WriteLine("Adult");
}
```

## Value Parameters

By default, value types are passed by value, meaning the method receives a copy.

```csharp
void ChangeNumber(int x)
{
    x = 100;
}

int number = 10;
ChangeNumber(number);

Console.WriteLine(number); // 10
```

## `ref`

`ref` allows a method to modify the original variable.

```csharp
void ChangeNumber(ref int x)
{
    x = 100;
}

int number = 10;
ChangeNumber(ref number);

Console.WriteLine(number); // 100
```

The variable must be initialized before being passed with `ref`.

## `out`

`out` allows a method to assign a value to a variable through the parameter.

```csharp
void GetNumber(out int number)
{
    number = 100;
}

int value;
GetNumber(out value);

Console.WriteLine(value); // 100
```

The variable does not need to be initialized before the call, but the method must assign it.

### `ref` vs `out`

```text
ref → I already have a value; modify my original variable.
out → I don't have a value; give me one.
```

## Optional Parameters

Optional parameters have default values.

```csharp
void Greet(string name = "Guest")
{
    Console.WriteLine("Hello " + name);
}

Greet();
Greet("Raj");
```

## Named Arguments

Arguments can be supplied by parameter name.

```csharp
void DisplayEmployee(string name, int age)
{
    Console.WriteLine(name);
    Console.WriteLine(age);
}

DisplayEmployee(age: 23, name: "Raj");
```

## Method Overloading

Method overloading means multiple methods have the same name but different parameter lists.

```csharp
int Add(int a, int b)
{
    return a + b;
}

int Add(int a, int b, int c)
{
    return a + b + c;
}
```

Changing only the return type is not valid overloading.

## `static` Methods

A `static` method belongs to the class/type rather than an individual object.

```csharp
class Calculator
{
    public static int Add(int a, int b)
    {
        return a + b;
    }
}
```

It can be called using the class name:

```csharp
int result = Calculator.Add(10, 20);
```

---

# 2. Arrays

## What is an Array?

An array stores multiple values of the same data type in a fixed-size collection.

```csharp
int[] numbers = { 10, 20, 30 };
```

## Single-Dimensional Array

```csharp
int[] numbers = new int[5];
```

Or:

```csharp
int[] numbers = { 10, 20, 30, 40, 50 };
```

## Array Indexing

Array indexes start at `0`.

```text
Index → Value

0 → 10
1 → 20
2 → 30
3 → 40
4 → 50
```

```csharp
Console.WriteLine(numbers[0]); // 10
```

## `Length`

`Length` gives the number of elements in an array.

```csharp
Console.WriteLine(numbers.Length);
```

For five elements:

```text
Length = 5
Last valid index = 4
```

## Multidimensional Arrays

A multidimensional array can represent rows and columns.

```csharp
int[,] numbers =
{
    { 10, 20, 30 },
    { 40, 50, 60 }
};

Console.WriteLine(numbers[1, 2]); // 60
```

---

# 3. Collections

## `List<T>`

`List<T>` is a dynamic collection.

```csharp
List<string> names = new List<string>();

names.Add("Raj");
names.Add("Amit");
names.Add("Rahul");
```

Unlike an array, a list can grow or shrink dynamically.

## `Add()`

Adds an element.

```csharp
numbers.Add(10);
```

## `Remove()`

Removes an element by value.

```csharp
numbers.Remove(30);
```

## `RemoveAt()`

Removes an element by index.

```csharp
numbers.RemoveAt(2);
```

### Difference

```text
Remove(30) → removes value 30
RemoveAt(2) → removes element at index 2
```

## `Contains()`

Checks whether a collection contains a value.

```csharp
names.Contains("Raj");
```

Returns `true` or `false`.

## `Count`

`Count` gives the number of elements currently in a `List<T>`.

```csharp
Console.WriteLine(names.Count);
```

Remember:

```text
Array → Length
List<T> → Count
```

## `Clear()`

Removes all elements.

```csharp
names.Clear();
```

## Dictionary

A `Dictionary<TKey, TValue>` stores key-value pairs.

```csharp
Dictionary<int, string> employees =
    new Dictionary<int, string>();

employees.Add(101, "Raj");
employees.Add(102, "Amit");
employees.Add(103, "Rahul");
```

Access by key:

```csharp
Console.WriteLine(employees[101]);
```

Dictionary keys must be unique because the key uniquely identifies the value.

## HashSet

A `HashSet<T>` stores unique values.

```csharp
HashSet<int> numbers = new HashSet<int>();

numbers.Add(10);
numbers.Add(20);
numbers.Add(10);
```

The duplicate `10` is not added.

## `foreach`

`foreach` iterates through each element of a collection.

```csharp
foreach (string name in names)
{
    Console.WriteLine(name);
}
```

With a dictionary:

```csharp
foreach (var employee in employees)
{
    Console.WriteLine(employee.Key + " - " + employee.Value);
}
```

### Collection Mental Model

```text
Array
→ Fixed size

List<T>
→ Dynamic collection

Dictionary<TKey,TValue>
→ Key-value mapping

HashSet<T>
→ Unique values
```

---

# 4. Classes and Objects

## Class

A class is a blueprint/type that defines data and behavior.

```csharp
class Employee
{
    public int Id;
    public string Name;
    public double Salary;
}
```

## Object

An object is an instance of a class.

```csharp
Employee emp = new Employee();

emp.Id = 101;
emp.Name = "Raj";
emp.Salary = 50000;
```

Remember:

```text
Class → Blueprint
Object → Actual instance
```

---

# 5. Fields

A field is a variable declared inside a class.

```csharp
class Employee
{
    public int Id;
    public string Name;
    public double Salary;
}
```

`Id`, `Name`, and `Salary` are fields.

---

# 6. Properties

A property provides a controlled way to read and/or modify data.

```csharp
class Employee
{
    public string Name { get; set; }
    public int Age { get; set; }
}
```

```text
get → read
set → write/change
```

Example:

```csharp
Employee emp = new Employee();

emp.Name = "Raj";          // set
Console.WriteLine(emp.Name); // get
```

## Auto-Implemented Property

```csharp
public string Name { get; set; }
```

C# provides the backing storage automatically.

## Property with Validation

```csharp
class Employee
{
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
}
```

`value` represents the value being assigned to the property.

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

No `set` means outside code cannot assign to it.

## Private Setter

```csharp
public int Id { get; private set; }
```

The property can be read publicly, but only the class can set it.

### Field vs Property

```text
Field
→ directly stores data

Property
→ provides controlled access to data
```

---

# 7. Constructors

A constructor is a special member that executes automatically when an object is created.

Characteristics:

- Same name as the class
- No return type
- Runs when an object is created

## Default/Parameterless Constructor

```csharp
class Employee
{
    public Employee()
    {
        Console.WriteLine("Employee created");
    }
}
```

## Parameterized Constructor

```csharp
class Employee
{
    public int Id;
    public string Name;

    public Employee(int id, string name)
    {
        Id = id;
        Name = name;
    }
}
```

Create the object:

```csharp
Employee emp = new Employee(101, "Raj");
```

---

# 8. `this`

`this` refers to the current object.

It is commonly used when parameter names and fields have the same name.

```csharp
class Employee
{
    private int id;

    public Employee(int id)
    {
        this.id = id;
    }
}
```

Here:

```text
id → constructor parameter
this.id → current object's field
```

---

# 9. Access Modifiers

Access modifiers control who can access a class member.

## `public`

Accessible broadly.

```csharp
public string Name;
```

## `private`

Accessible only inside the same class.

```csharp
private double salary;
```

## `protected`

Accessible inside the class and derived classes.

```csharp
protected string name;
```

## `internal`

Accessible within the same assembly.

```csharp
internal class Employee
{
}
```

### Mental Model

```text
private
→ only the class

protected
→ class + derived classes

internal
→ same assembly

public
→ broadly accessible
```

---

# 10. Encapsulation

Encapsulation means bundling data and behavior together while controlling access to internal data.

Example:

```csharp
class BankAccount
{
    private double balance;

    public void Deposit(double amount)
    {
        if (amount > 0)
        {
            balance += amount;
        }
    }

    public double GetBalance()
    {
        return balance;
    }
}
```

Outside code cannot directly modify `balance`.

```text
Encapsulation
→ protect internal data
→ provide controlled access
```

---

# 11. Abstraction

Abstraction means exposing necessary functionality while hiding implementation details.

Common C# mechanisms:

```text
abstract classes
interfaces
```

Example:

```csharp
interface IPayment
{
    void Pay();
}
```

The interface defines what must be provided without specifying the implementation.

---

# 12. Inheritance

Inheritance allows a derived class to acquire members from a base class.

```csharp
class Animal
{
    public void Eat()
    {
        Console.WriteLine("Eating");
    }
}

class Dog : Animal
{
}
```

Now:

```csharp
Dog dog = new Dog();
dog.Eat();
```

Terminology:

```text
Animal → Base/Parent class
Dog → Derived/Child class
```

---

# 13. Polymorphism

Polymorphism means one concept can have multiple forms.

Two important forms:

```text
Compile-time polymorphism
→ Method overloading

Runtime polymorphism
→ Method overriding
```

---

# 14. Method Overriding

A derived class provides its own implementation of a base class method.

```csharp
class Animal
{
    public virtual void Sound()
    {
        Console.WriteLine("Animal sound");
    }
}

class Dog : Animal
{
    public override void Sound()
    {
        Console.WriteLine("Bark");
    }
}
```

---

# 15. `virtual` and `override`

`virtual` is used in the base class to allow overriding.

```csharp
public virtual void Sound()
{
    Console.WriteLine("Animal sound");
}
```

`override` is used in the derived class to replace the inherited implementation.

```csharp
public override void Sound()
{
    Console.WriteLine("Bark");
}
```

Remember:

```text
Base class → virtual
Derived class → override
```

---

# 16. `abstract`

An abstract class cannot be instantiated directly.

```csharp
abstract class Animal
{
    public abstract void Sound();
}
```

An abstract method has no implementation in the abstract base class.

The derived class must implement it:

```csharp
class Dog : Animal
{
    public override void Sound()
    {
        Console.WriteLine("Bark");
    }
}
```

---

# 17. Interfaces

An interface defines a contract that implementing classes must follow.

```csharp
interface IPayment
{
    void Pay();
}
```

Implementation:

```csharp
class Razorpay : IPayment
{
    public void Pay()
    {
        Console.WriteLine("Payment successful");
    }
}
```

Different classes can implement the same interface differently.

---

# 18. Base and Derived Classes

```csharp
class Animal
{
}

class Dog : Animal
{
}
```

```text
Animal → Base class
Dog → Derived class
```

---

# 19. `base`

`base` refers to the base-class part of the current object.

## Calling a Base Constructor

```csharp
class Animal
{
    public Animal(string name)
    {
        Console.WriteLine(name);
    }
}

class Dog : Animal
{
    public Dog(string name) : base(name)
    {
    }
}
```

## Calling a Base Method

```csharp
class Animal
{
    public virtual void Sound()
    {
        Console.WriteLine("Animal sound");
    }
}

class Dog : Animal
{
    public override void Sound()
    {
        base.Sound();
        Console.WriteLine("Bark");
    }
}
```

---

# 20. `sealed`

A sealed class cannot be inherited.

```csharp
sealed class Dog
{
}
```

This is invalid:

```csharp
class Puppy : Dog
{
}
```

A method can also be sealed after overriding it:

```csharp
public sealed override void Sound()
{
    Console.WriteLine("Bark");
}
```

A further derived class cannot override that method.

---

# Complete OOP Practice Example

The following example combines classes, properties, constructors, encapsulation, inheritance, polymorphism, interfaces, `base`, `virtual`, and `override`.

```csharp
using System;

interface IEmployeeCard
{
    void GenerateCard();
}

class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }

    private double salary;

    public double Salary
    {
        get
        {
            return salary;
        }

        private set
        {
            if (value >= 0)
            {
                salary = value;
            }
        }
    }

    public Employee(int id, string name, double salary)
    {
        Id = id;
        Name = name;
        Salary = salary;
    }

    public void DisplayDetails()
    {
        Console.WriteLine($"ID: {Id}");
        Console.WriteLine($"Name: {Name}");
        Console.WriteLine($"Salary: {Salary}");
    }

    public virtual double CalculateBonus()
    {
        return Salary * 0.10;
    }
}

class PermanentEmployee : Employee, IEmployeeCard
{
    public PermanentEmployee(int id, string name, double salary)
        : base(id, name, salary)
    {
    }

    public override double CalculateBonus()
    {
        return Salary * 0.20;
    }

    public void GenerateCard()
    {
        Console.WriteLine($"Employee Card: {Id} - {Name}");
    }
}

class ContractEmployee : Employee, IEmployeeCard
{
    public ContractEmployee(int id, string name, double salary)
        : base(id, name, salary)
    {
    }

    public override double CalculateBonus()
    {
        return Salary * 0.10;
    }

    public void GenerateCard()
    {
        Console.WriteLine($"Employee Card: {Id} - {Name}");
    }
}

class Program
{
    static void Main()
    {
        PermanentEmployee permanentEmployee =
            new PermanentEmployee(101, "Raj", 50000);

        ContractEmployee contractEmployee =
            new ContractEmployee(102, "Amit", 40000);

        permanentEmployee.DisplayDetails();
        Console.WriteLine(
            $"Bonus: {permanentEmployee.CalculateBonus()}"
        );
        permanentEmployee.GenerateCard();

        Console.WriteLine();

        contractEmployee.DisplayDetails();
        Console.WriteLine(
            $"Bonus: {contractEmployee.CalculateBonus()}"
        );
        contractEmployee.GenerateCard();
    }
}
```

---

# Quick Revision Checklist — 24/08/2026

## Methods

- [ ] Explain what a method is
- [ ] Declare and call methods
- [ ] Explain parameters vs arguments
- [ ] Explain return types and `void`
- [ ] Use `return`
- [ ] Explain value parameters
- [ ] Use `ref`
- [ ] Use `out`
- [ ] Use optional parameters
- [ ] Use named arguments
- [ ] Explain method overloading
- [ ] Explain `static` methods

## Arrays & Collections

- [ ] Explain arrays
- [ ] Explain array indexing
- [ ] Use `Length`
- [ ] Understand multidimensional arrays
- [ ] Use `List<T>`
- [ ] Use `Add()`
- [ ] Use `Remove()`
- [ ] Use `RemoveAt()`
- [ ] Use `Contains()`
- [ ] Use `Count`
- [ ] Use `Clear()`
- [ ] Explain `Dictionary<TKey,TValue>`
- [ ] Explain `HashSet<T>`
- [ ] Use `foreach`

## OOP

- [ ] Explain classes
- [ ] Explain objects
- [ ] Explain fields
- [ ] Explain properties
- [ ] Explain constructors
- [ ] Explain default and parameterized constructors
- [ ] Explain `this`
- [ ] Explain `public`
- [ ] Explain `private`
- [ ] Explain `protected`
- [ ] Explain `internal`
- [ ] Explain encapsulation
- [ ] Explain abstraction
- [ ] Explain inheritance
- [ ] Explain polymorphism
- [ ] Explain method overloading vs overriding
- [ ] Explain `virtual`
- [ ] Explain `override`
- [ ] Explain `abstract`
- [ ] Explain interfaces
- [ ] Explain base and derived classes
- [ ] Explain `base`
- [ ] Explain `sealed`
- [ ] Build a small OOP program combining these concepts

---

## Progress

```text
21/08/2026
C# Fundamentals
        ↓
24/08/2026
Methods
        ↓
Arrays & Collections
        ↓
OOP
```
