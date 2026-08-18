# C# Basics

## 1. C# and .NET

### C# vs .NET

-   **C#** is the programming language.
-   **.NET** is the development platform/runtime and class-library
    ecosystem used to build and run C# applications.
-   **ASP.NET Web Forms** is a web framework built on the .NET Framework
    and uses C# for code-behind.

Conceptually:

``` text
C# Code
   ↓
C# Compiler
   ↓
IL / MSIL
   ↓
.NET CLR
   ↓
Machine Code
```

For an ASP.NET Web Forms application:

``` text
Browser
   ↓
ASP.NET Web Forms
   ↓
C# Code-behind
   ↓
Business Logic
   ↓
ADO.NET / Database
   ↓
SQL Server
```

------------------------------------------------------------------------

# 2. Basic C# Syntax

## Program Structure

``` csharp
using System;

class Program
{
    static void Main()
    {
        Console.WriteLine("Hello World");
    }
}
```

### Important parts

-   `using System;` --- imports a namespace.
-   `class Program` --- defines a class.
-   `static void Main()` --- traditional console application entry
    point.
-   `Console.WriteLine()` --- writes output.

------------------------------------------------------------------------

# 3. Variables and Data Types

A variable stores a value.

``` csharp
int age = 22;
string name = "Raj";
double salary = 50000.50;
bool isDeveloper = true;
char grade = 'A';
```

Common types:

  Type        Example
  ----------- ------------------
  `int`       `22`
  `double`    `22.5`
  `float`     `22.5f`
  `decimal`   `50000.50m`
  `char`      `'A'`
  `string`    `"Raj"`
  `bool`      `true` / `false`

`char` stores one character; `string` stores a sequence of characters.

------------------------------------------------------------------------

# 4. Output and String Interpolation

``` csharp
string name = "Raj";
int age = 22;

Console.WriteLine(name);
Console.WriteLine(age);
```

String concatenation:

``` csharp
Console.WriteLine("Name: " + name);
```

String interpolation:

``` csharp
Console.WriteLine($"Name: {name}");
Console.WriteLine($"Age: {age}");
```

`$` allows expressions inside `{}` to be evaluated and inserted into the
string.

------------------------------------------------------------------------

# 5. Conditions

## if / else

``` csharp
int age = 22;

if (age >= 18)
{
    Console.WriteLine("Adult");
}
else
{
    Console.WriteLine("Minor");
}
```

## else if

``` csharp
int marks = 85;

if (marks >= 90)
{
    Console.WriteLine("A+");
}
else if (marks >= 75)
{
    Console.WriteLine("A");
}
else
{
    Console.WriteLine("B");
}
```

------------------------------------------------------------------------

# 6. Methods

A method contains reusable behavior.

``` csharp
static void SayHello()
{
    Console.WriteLine("Hello");
}
```

Call it:

``` csharp
SayHello();
```

## Parameters

``` csharp
static void Greet(string name)
{
    Console.WriteLine($"Hello {name}");
}
```

Call:

``` csharp
Greet("Raj");
```

-   `name` is a **parameter**.
-   `"Raj"` is an **argument**.

## Return values

``` csharp
static int Add(int a, int b)
{
    return a + b;
}
```

Usage:

``` csharp
int result = Add(10, 20);
```

General method structure:

``` csharp
access_modifier return_type MethodName(parameters)
{
    // method body
}
```

Example:

``` csharp
public int CalculateSalary(int basicSalary, int bonus)
{
    return basicSalary + bonus;
}
```

------------------------------------------------------------------------

# 7. Classes and Objects

A **class** is a blueprint.

``` csharp
class User
{
    public string Name { get; set; }
    public int Age { get; set; }
}
```

An **object** is an instance created from the class.

``` csharp
User user = new User();
```

You can create multiple objects:

``` csharp
User user1 = new User();
User user2 = new User();
```

Mental model:

``` text
Class  = Blueprint
Object = Actual instance
```

------------------------------------------------------------------------

# 8. Fields

A field is a variable declared inside a class.

``` csharp
class User
{
    private string name;
    private int age;
}
```

Fields normally represent an object's internal state.

------------------------------------------------------------------------

# 9. Properties

Properties provide controlled access to data.

``` csharp
class User
{
    public string Name { get; set; }
    public int Age { get; set; }
}
```

`get` reads a value.

`set` assigns/changes a value.

``` csharp
User user = new User();

user.Name = "Raj";              // set
Console.WriteLine(user.Name);   // get
```

A useful pattern:

``` csharp
public double Balance { get; private set; }
```

This means:

-   Outside code can read `Balance`.
-   Outside code cannot directly change `Balance`.
-   The class itself can change it.

------------------------------------------------------------------------

# 10. Constructors

A constructor runs when an object is created.

``` csharp
class User
{
    public string Name { get; set; }
    public int Age { get; set; }

    public User(string name, int age)
    {
        Name = name;
        Age = age;
    }
}
```

Create the object:

``` csharp
User user = new User("Raj", 22);
```

Flow:

``` text
new User("Raj", 22)
        ↓
Constructor
        ↓
Name = "Raj"
Age = 22
        ↓
Object created
```

## Constructor overloading

A class can have multiple constructors.

``` csharp
class User
{
    public string Name { get; set; }
    public int Age { get; set; }

    public User()
    {
    }

    public User(string name)
    {
        Name = name;
    }

    public User(string name, int age)
    {
        Name = name;
        Age = age;
    }
}
```

## `this`

When a parameter and field/property have the same name:

``` csharp
class User
{
    private string name;

    public User(string name)
    {
        this.name = name;
    }
}
```

`this.name` refers to the current object's field.

------------------------------------------------------------------------

# 11. Access Modifiers

Access modifiers control visibility.

Main ones to know:

  Modifier        Same Class   Derived Class   Outside
  ------------- ------------ --------------- ---------
  `public`               Yes             Yes       Yes
  `private`              Yes              No        No
  `protected`            Yes             Yes        No
  `internal`             Yes           Yes\*     Yes\*

`internal` is primarily accessible within the same assembly.

## public

``` csharp
public string Name;
```

Accessible from outside the class.

## private

``` csharp
private string password;
```

Accessible only inside the class.

## protected

``` csharp
protected string name;
```

Accessible inside the class and derived classes.

`protected` is especially relevant in ASP.NET Web Forms code such as:

``` csharp
protected void Page_Load(object sender, EventArgs e)
{
}
```

------------------------------------------------------------------------

# 12. Encapsulation

Encapsulation means protecting internal state and exposing controlled
ways to interact with it.

Bad design:

``` csharp
class BankAccount
{
    public double Balance;
}
```

Anyone can do:

``` csharp
account.Balance = -500000;
```

Better:

``` csharp
class BankAccount
{
    public double Balance { get; private set; }

    public void Deposit(double amount)
    {
        if (amount > 0)
        {
            Balance += amount;
        }
    }
}
```

Usage:

``` csharp
BankAccount account = new BankAccount();

account.Deposit(5000);
Console.WriteLine(account.Balance);
```

The internal state is controlled by the class.

------------------------------------------------------------------------

# 13. Static vs Instance

## Instance members

Instance members belong to individual objects.

``` csharp
class User
{
    public string Name;

    public void SayHello()
    {
        Console.WriteLine(Name);
    }
}
```

Usage:

``` csharp
User user = new User();

user.Name = "Raj";
user.SayHello();
```

Each object has its own `Name`.

## Static members

`static` members belong to the class itself.

``` csharp
class MathHelper
{
    public static int Add(int a, int b)
    {
        return a + b;
    }
}
```

Usage:

``` csharp
int result = MathHelper.Add(10, 20);
```

No object is required.

### Static field

``` csharp
class User
{
    public string Name;
    public static int UserCount;
}
```

`Name` belongs to each object.

`UserCount` belongs to the class.

------------------------------------------------------------------------

# 14. Inheritance

Inheritance allows one class to reuse/extend another class.

``` csharp
class Animal
{
    public string Name { get; set; }

    public void Eat()
    {
        Console.WriteLine("Eating...");
    }
}
```

Derived class:

``` csharp
class Dog : Animal
{
    public void Bark()
    {
        Console.WriteLine("Woof!");
    }
}
```

Usage:

``` csharp
Dog dog = new Dog();

dog.Name = "Bruno";
dog.Eat();
dog.Bark();
```

`Dog` inherits `Name` and `Eat()` from `Animal`.

------------------------------------------------------------------------

# 15. Polymorphism

Polymorphism means one abstraction can have multiple
forms/implementations.

## Compile-time polymorphism --- method overloading

``` csharp
class Calculator
{
    public int Add(int a, int b)
    {
        return a + b;
    }

    public int Add(int a, int b, int c)
    {
        return a + b + c;
    }
}
```

The method name is the same, but parameters differ.

A method cannot be overloaded only by changing its return type.

## Runtime polymorphism --- overriding

Base class:

``` csharp
class Animal
{
    public virtual void MakeSound()
    {
        Console.WriteLine("Some sound");
    }
}
```

Derived classes:

``` csharp
class Dog : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Woof");
    }
}

class Cat : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Meow");
    }
}
```

Usage:

``` csharp
Animal animal1 = new Dog();
Animal animal2 = new Cat();

animal1.MakeSound();
animal2.MakeSound();
```

Output:

``` text
Woof
Meow
```

`virtual` allows overriding.

`override` provides the derived implementation.

------------------------------------------------------------------------

# 16. Abstraction

Abstraction means exposing required behavior while hiding implementation
details.

## Abstract class

``` csharp
abstract class Animal
{
    public abstract void MakeSound();
}
```

A derived class must implement the abstract method:

``` csharp
class Dog : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Woof");
    }
}
```

You cannot instantiate an abstract class:

``` csharp
Animal animal = new Animal(); // Invalid
```

But you can:

``` csharp
Animal animal = new Dog();
```

------------------------------------------------------------------------

# 17. Interfaces

An interface defines a contract.

``` csharp
interface IPaymentService
{
    void Pay(double amount);
}
```

Implementation:

``` csharp
class RazorpayPaymentService : IPaymentService
{
    public void Pay(double amount)
    {
        Console.WriteLine($"Paying {amount} using Razorpay");
    }
}
```

Another implementation:

``` csharp
class StripePaymentService : IPaymentService
{
    public void Pay(double amount)
    {
        Console.WriteLine($"Paying {amount} using Stripe");
    }
}
```

The calling code can depend on:

``` csharp
IPaymentService paymentService;
```

rather than a specific implementation.

Useful mental model:

``` text
Abstract class → What are you?
Interface      → What can you do?
```

------------------------------------------------------------------------

# 18. Four OOP Pillars

``` text
Encapsulation
    ↓
Protect internal state

Inheritance
    ↓
Reuse/extend base classes

Polymorphism
    ↓
Same abstraction, different behavior

Abstraction
    ↓
Expose what is needed, hide implementation
```

------------------------------------------------------------------------

# 19. Arrays

``` csharp
int[] numbers = { 10, 20, 30, 40, 50 };
```

Access by index:

``` csharp
Console.WriteLine(numbers[0]);
```

Indexes start at `0`.

``` text
Index:  0   1   2   3   4
Value: 10  20  30  40  50
```

Length:

``` csharp
numbers.Length;
```

------------------------------------------------------------------------

# 20. `foreach`

``` csharp
foreach (int number in numbers)
{
    Console.WriteLine(number);
}
```

`foreach` is commonly used to iterate over collections.

------------------------------------------------------------------------

# 21. `List<T>`

Arrays have fixed size. `List<T>` is a dynamically sized collection.

``` csharp
List<int> numbers = new List<int>();

numbers.Add(10);
numbers.Add(20);
numbers.Add(30);
```

Generic syntax:

``` csharp
List<int>
List<string>
List<User>
```

`T` represents the element type.

Example:

``` csharp
List<User> users = new List<User>();

users.Add(new User("Raj", 22));
users.Add(new User("Amit", 25));
```

------------------------------------------------------------------------

# 22. Generics

Generics allow reusable, type-safe code.

``` csharp
class Box<T>
{
    public T Value { get; set; }
}
```

Usage:

``` csharp
Box<int> intBox = new Box<int>();
intBox.Value = 100;

Box<string> stringBox = new Box<string>();
stringBox.Value = "Hello";
```

`T` is a type placeholder.

Benefits:

-   Reusability
-   Compile-time type safety
-   Less casting
-   Cleaner APIs

------------------------------------------------------------------------

# 23. Dictionary

A dictionary stores key-value pairs.

``` csharp
Dictionary<int, string> students =
    new Dictionary<int, string>();

students.Add(1, "Raj");
students.Add(2, "Amit");
students.Add(3, "Vedant");
```

Access:

``` csharp
Console.WriteLine(students[1]);
```

Concept:

``` text
Key → Value

1 → Raj
2 → Amit
3 → Vedant
```

------------------------------------------------------------------------

# 24. `var`

`var` uses compile-time type inference.

``` csharp
var name = "Raj";
```

The compiler knows `name` is a `string`.

``` csharp
var age = 22;
```

The compiler knows `age` is an `int`.

`var` is still strongly typed.

It does NOT mean "unknown type."

------------------------------------------------------------------------

# 25. LINQ

LINQ = Language Integrated Query.

Example:

``` csharp
List<int> numbers =
    new List<int> { 10, 15, 20, 25, 30, 35 };

var result = numbers.Where(number => number > 20);
```

Result:

``` text
25
30
35
```

Common LINQ methods:

  Method                  Purpose
  ----------------------- -------------------------
  `Where()`               Filter
  `Select()`              Transform/project
  `First()`               First element
  `FirstOrDefault()`      First or default
  `Single()`              Exactly one
  `Any()`                 Check whether any match
  `Count()`               Count elements
  `OrderBy()`             Sort ascending
  `OrderByDescending()`   Sort descending
  `ToList()`              Convert to `List<T>`

Example:

``` csharp
var adultNames = users
    .Where(u => u.Age >= 18)
    .Select(u => u.Name)
    .ToList();
```

------------------------------------------------------------------------

# 26. Lambda Expressions

A lambda is a compact way to represent a function/expression.

``` csharp
x => x > 20
```

means approximately:

``` text
Take x
  ↓
Check whether x > 20
```

Example:

``` csharp
var result = numbers.Where(x => x > 20);
```

Lambdas are heavily used by LINQ.

------------------------------------------------------------------------

# 27. Exception Handling

Exceptions represent errors that occur during execution.

``` csharp
try
{
    int number = int.Parse("hello");
}
catch (Exception ex)
{
    Console.WriteLine(ex.Message);
}
finally
{
    Console.WriteLine("Cleanup");
}
```

Flow:

``` text
try
 ↓
Exception?
 ↓
catch
 ↓
finally
```

You can also throw an exception:

``` csharp
throw new ArgumentException("Invalid age");
```

Common exception types include:

-   `Exception`
-   `ArgumentException`
-   `InvalidOperationException`
-   `NullReferenceException`
-   `FormatException`

------------------------------------------------------------------------

# 28. Nullable Value Types

Normally:

``` csharp
int age = 22;
```

cannot contain `null`.

Nullable:

``` csharp
int? age = null;
```

This means:

``` text
int OR null
```

Check:

``` csharp
if (age.HasValue)
{
    Console.WriteLine(age.Value);
}
```

Null-coalescing operator:

``` csharp
int actualAge = age ?? 0;
```

This is particularly useful with database values because SQL columns can
contain `NULL`.

------------------------------------------------------------------------

# 29. Value Types vs Reference Types

## Value types

Examples:

-   `int`
-   `double`
-   `bool`
-   `char`
-   `struct`
-   `enum`

Example:

``` csharp
int a = 10;
int b = a;

b = 20;
```

Result:

``` text
a = 10
b = 20
```

`b` received a copy of the value.

## Reference types

Examples:

-   `class`
-   `object`
-   `array`
-   `delegate`

Example:

``` csharp
class User
{
    public string Name { get; set; }
}

User user1 = new User();
user1.Name = "Raj";

User user2 = user1;
user2.Name = "Amit";
```

Now:

``` csharp
Console.WriteLine(user1.Name);
```

prints:

``` text
Amit
```

Both variables refer to the same object.

------------------------------------------------------------------------

# 30. `ref`

Normally, a value parameter receives a copy.

``` csharp
static void Change(int x)
{
    x = 100;
}

int number = 10;

Change(number);

// number is still 10
```

With `ref`:

``` csharp
static void Change(ref int x)
{
    x = 100;
}

int number = 10;

Change(ref number);

// number is now 100
```

`ref` allows a method to work with the caller's variable directly.

------------------------------------------------------------------------

# 31. `out`

`out` allows a method to return additional values through parameters.

``` csharp
static void GetUser(out string name, out int age)
{
    name = "Raj";
    age = 22;
}
```

Usage:

``` csharp
string name;
int age;

GetUser(out name, out age);
```

Classic example:

``` csharp
int number;

bool success = int.TryParse("123", out number);
```

The method returns:

``` text
success → whether parsing succeeded
number  → parsed value
```

------------------------------------------------------------------------

# 32. Delegates

A delegate is a type-safe reference to a method.

``` csharp
delegate void MyDelegate();

static void SayHello()
{
    Console.WriteLine("Hello");
}
```

Usage:

``` csharp
MyDelegate d = SayHello;

d();
```

Delegates are foundational to:

-   Events
-   Callbacks
-   Lambdas
-   LINQ

------------------------------------------------------------------------

# 33. `IEnumerable<T>`

`IEnumerable<T>` represents something that can be enumerated.

``` csharp
IEnumerable<int> numbers =
    new List<int> { 10, 20, 30 };
```

You can use:

``` csharp
foreach (int number in numbers)
{
    Console.WriteLine(number);
}
```

A `List<T>` implements `IEnumerable<T>`.

Mental model:

``` text
List<T>
   ↓
IEnumerable<T>
   ↓
Can be enumerated
```

------------------------------------------------------------------------

# 34. `IQueryable<T>`

`IQueryable<T>` is commonly used with query providers and databases.

Conceptually:

``` text
IEnumerable
→ primarily enumerate/query data already available

IQueryable
→ represents a query that can be translated/executed
   by a data source
```

For example, with an ORM:

``` csharp
IQueryable<User> users = db.Users;

var result = users
    .Where(u => u.Age > 18);
```

The exact execution behavior depends on the query provider.

------------------------------------------------------------------------

# 35. SOLID Principles

SOLID helps design maintainable C# applications.

``` text
S → Single Responsibility
O → Open/Closed
L → Liskov Substitution
I → Interface Segregation
D → Dependency Inversion
```

## S --- Single Responsibility

A class should have one responsibility/reason to change.

Bad:

``` csharp
class Employee
{
    void SaveToDatabase() { }
    void GenerateReport() { }
    void SendEmail() { }
}
```

Better:

``` text
Employee
EmployeeRepository
EmployeeReportService
EmailService
```

## O --- Open/Closed

Software should be open for extension but closed for modification.

Interfaces/strategies can help:

``` csharp
interface IDiscountStrategy
{
    double Calculate(double price);
}
```

Different discount types can implement the interface without constantly
modifying existing code.

## L --- Liskov Substitution

Derived classes should be usable where their base type is expected
without breaking expected behavior.

Avoid inheritance relationships where the child cannot properly fulfill
the base contract.

## I --- Interface Segregation

Don't force classes to implement methods they don't need.

Prefer smaller, focused interfaces.

## D --- Dependency Inversion

High-level code should depend on abstractions rather than concrete
implementations.

Bad:

``` csharp
class OrderService
{
    private SqlOrderRepository repository;

    public OrderService()
    {
        repository = new SqlOrderRepository();
    }
}
```

Better:

``` csharp
class OrderService
{
    private readonly IOrderRepository repository;

    public OrderService(IOrderRepository repository)
    {
        this.repository = repository;
    }
}
```

This leads directly to Dependency Injection.

------------------------------------------------------------------------

# 36. Dependency Injection

A **dependency** is an object/class another class needs to perform its
work.

Tightly coupled:

``` csharp
class OrderService
{
    private SqlOrderRepository repository;

    public OrderService()
    {
        repository = new SqlOrderRepository();
    }
}
```

`OrderService` creates its own dependency.

With Dependency Injection:

``` csharp
class OrderService
{
    private readonly IOrderRepository repository;

    public OrderService(IOrderRepository repository)
    {
        this.repository = repository;
    }
}
```

The dependency is supplied from outside.

Manual DI:

``` csharp
IOrderRepository repository =
    new SqlOrderRepository();

OrderService service =
    new OrderService(repository);
```

Important distinction:

``` text
Dependency Inversion
→ Design principle

Dependency Injection
→ Technique for supplying dependencies

DI Container
→ Tool that creates/wires dependencies automatically
```

Constructor injection is the most common form.

------------------------------------------------------------------------

# 37. .NET Fundamentals

## CLR

CLR = Common Language Runtime.

It executes managed .NET code and provides services such as:

-   Garbage collection
-   Exception handling
-   Memory management
-   Type safety
-   Thread management

## .NET Framework

For an ASP.NET Web Forms application, the traditional stack is:

``` text
.NET Framework
   │
   ├── CLR
   ├── Base Class Libraries
   └── ASP.NET
          └── Web Forms
```

Don't confuse classic ASP.NET Web Forms with ASP.NET Core.

------------------------------------------------------------------------

# 38. Namespaces

Namespaces organize and qualify types.

``` csharp
namespace MyCompany.Models
{
    public class Employee
    {
        public string Name { get; set; }
    }
}
```

Use:

``` csharp
using MyCompany.Models;
```

Then:

``` csharp
Employee employee = new Employee();
```

Without `using`:

``` csharp
MyCompany.Models.Employee employee =
    new MyCompany.Models.Employee();
```

Common namespaces:

``` csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Data;
```

------------------------------------------------------------------------

# 39. Assemblies / DLLs

An assembly is a compiled unit of .NET code.

Example:

``` text
MyCompany.Web.dll
MyCompany.Business.dll
MyCompany.Data.dll
```

Conceptually:

``` text
Source Code
    ↓
Compiler
    ↓
Assembly (.dll / .exe)
```

Assemblies can contain classes, interfaces, metadata, and references to
dependencies.

------------------------------------------------------------------------

# 40. ASP.NET Web Forms

Web Forms uses C# for its code-behind.

Typical files:

``` text
Employee.aspx
Employee.aspx.cs
```

`.aspx` contains the UI/markup:

``` aspx
<asp:TextBox ID="txtName" runat="server" />

<asp:Button
    ID="btnSave"
    runat="server"
    Text="Save"
    OnClick="btnSave_Click" />
```

`.aspx.cs` contains C#:

``` csharp
protected void btnSave_Click(object sender, EventArgs e)
{
    string name = txtName.Text;
}
```

Web Forms does not introduce a new programming language. It uses C# plus
the ASP.NET Web Forms framework.

------------------------------------------------------------------------

# 41. Web Forms Controls

Common controls:

``` text
TextBox
Button
Label
DropDownList
CheckBox
GridView
```

Example:

``` aspx
<asp:TextBox ID="txtName" runat="server" />

<asp:Button
    ID="btnSave"
    runat="server"
    Text="Save"
    OnClick="btnSave_Click" />
```

Code-behind:

``` csharp
protected void btnSave_Click(object sender, EventArgs e)
{
    string name = txtName.Text;
}
```

------------------------------------------------------------------------

# 42. Request and Response

Browser:

``` text
HTTP Request
     ↓
ASP.NET Web Forms
     ↓
Page processing
     ↓
HTML Response
     ↓
Browser
```

C#:

``` csharp
string id = Request.QueryString["id"];
```

Response:

``` csharp
Response.Write("Hello");
```

------------------------------------------------------------------------

# 43. Web Forms Page Lifecycle

Important sequence:

``` text
HTTP Request
     ↓
Init
     ↓
Load ViewState
     ↓
Load PostBack Data
     ↓
Page_Load
     ↓
Control Events
     ↓
PreRender
     ↓
Save ViewState
     ↓
Render
     ↓
HTML Response
```

Example:

``` csharp
protected void Page_Load(object sender, EventArgs e)
{
}
```

Button event:

``` csharp
protected void btnSave_Click(object sender, EventArgs e)
{
}
```

------------------------------------------------------------------------

# 44. ViewState

Web is stateless. Web Forms uses ViewState to preserve certain
page/control state across postbacks.

``` csharp
ViewState["Name"] = "Raj";
```

Retrieve:

``` csharp
string name = ViewState["Name"] as string;
```

Conceptually:

``` text
Page state
   ↓
ViewState
   ↓
PostBack
   ↓
State restored
```

------------------------------------------------------------------------

# 45. Session

Session stores user/session-level server-side state.

``` csharp
Session["UserId"] = 101;
```

Retrieve:

``` csharp
int userId = (int)Session["UserId"];
```

Basic distinction:

``` text
ViewState
→ page/control state across postbacks

Session
→ user/session-level state, potentially across pages
```

------------------------------------------------------------------------

# 46. ADO.NET

ADO.NET is the traditional .NET data-access technology you'll encounter
in many Web Forms applications.

Important classes:

``` text
SqlConnection
SqlCommand
SqlDataReader
DataTable
DataSet
```

Typical flow:

``` text
C# Web Forms
     ↓
SqlConnection
     ↓
SqlCommand
     ↓
SQL Server
     ↓
Results
```

Example:

``` csharp
using (SqlConnection connection =
       new SqlConnection(connectionString))
{
    connection.Open();

    SqlCommand command =
        new SqlCommand(
            "SELECT Name FROM Employee",
            connection);

    SqlDataReader reader = command.ExecuteReader();

    while (reader.Read())
    {
        string name = reader["Name"].ToString();
    }
}
```

------------------------------------------------------------------------

# 47. Parameterized SQL

Avoid:

``` csharp
string sql =
    "SELECT * FROM Employee WHERE Id = " + id;
```

Prefer parameters:

``` csharp
SqlCommand command = new SqlCommand(
    "SELECT * FROM Employee WHERE Id = @Id",
    connection);

command.Parameters.AddWithValue("@Id", id);
```

In production code, prefer appropriately typed parameters instead of
relying heavily on `AddWithValue`.

Parameterized queries help prevent SQL injection and correctly handle
values.

------------------------------------------------------------------------

# 48. DataReader vs DataTable

## SqlDataReader

Reads results sequentially:

``` csharp
while (reader.Read())
{
    string name = reader["Name"].ToString();
}
```

## DataTable

Stores tabular data in memory:

``` csharp
DataTable table = new DataTable();

adapter.Fill(table);
```

Can be bound to a Web Forms control:

``` csharp
GridView1.DataSource = table;
GridView1.DataBind();
```

------------------------------------------------------------------------

# 49. Typical Web Forms Architecture

A common layered structure:

``` text
Browser
   ↓
Employee.aspx
   ↓
Employee.aspx.cs
   ↓
EmployeeService
   ↓
IEmployeeRepository
   ↓
EmployeeRepository
   ↓
ADO.NET
   ↓
SQL Server
```

Example model:

``` csharp
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

Interface:

``` csharp
public interface IEmployeeRepository
{
    Employee GetById(int id);
}
```

Repository:

``` csharp
public class EmployeeRepository : IEmployeeRepository
{
    public Employee GetById(int id)
    {
        // Database code
        return null;
    }
}
```

Service:

``` csharp
public class EmployeeService
{
    private readonly IEmployeeRepository repository;

    public EmployeeService(IEmployeeRepository repository)
    {
        this.repository = repository;
    }

    public Employee GetEmployee(int id)
    {
        return repository.GetById(id);
    }
}
```

Web Form:

``` csharp
protected void btnSearch_Click(object sender, EventArgs e)
{
    int id = int.Parse(txtId.Text);

    Employee employee = employeeService.GetEmployee(id);

    lblName.Text = employee.Name;
}
```

------------------------------------------------------------------------

# 50. The Mental Model to Remember

Your C#/.NET/Web Forms stack can be understood as:

``` text
                 C#
                  ↓
        Classes / Objects / OOP
                  ↓
          Interfaces / Generics
                  ↓
            Collections / LINQ
                  ↓
               SOLID
                  ↓
       Dependency Injection
                  ↓
           .NET Framework
                  ↓
          ASP.NET Web Forms
                  ↓
        Page + Controls + Events
                  ↓
              ADO.NET
                  ↓
             SQL Server
```

## What you should be able to explain now

Before moving to advanced topics, you should be comfortable explaining:

-   C# vs .NET
-   Class vs object
-   Field vs property
-   Constructor
-   `public` / `private` / `protected`
-   `static` vs instance
-   Encapsulation
-   Inheritance
-   Polymorphism
-   Abstract class vs interface
-   `List<T>` and generics
-   Dictionary
-   LINQ and lambdas
-   Exception handling
-   Value vs reference types
-   `ref` / `out`
-   Delegates
-   `IEnumerable` vs `IQueryable`
-   SOLID
-   Dependency Inversion vs Dependency Injection
-   CLR
-   Namespace and assembly
-   Web Forms controls
-   Page lifecycle
-   ViewState vs Session
-   Basic ADO.NET
-   Parameterized SQL
-   Service / Repository architecture

## Next learning phase

The next step should be **hands-on Dependency Injection + Web Forms
architecture**, followed by:

``` text
DI
 ↓
ADO.NET
 ↓
Stored Procedures
 ↓
Web Forms CRUD
 ↓
GridView / Forms
 ↓
Validation
 ↓
State management
 ↓
Real project architecture
 ↓
Debugging company-style code
```

This will turn the concepts above into practical skills for your C#/.NET
Web Forms role.
