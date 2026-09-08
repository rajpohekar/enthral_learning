# C# — Revision Notes
*Consolidated from `enthral_learning` repo (21–27 Aug 2026)*

---

## 1. C# vs .NET
- **C#** = the programming language.
- **.NET** = platform/runtime + class libraries used to build & run C# apps.
- **ASP.NET Web Forms** = a web framework built on .NET Framework, uses C# for code-behind.

**Execution pipeline:**
```
C# Source (.cs) → Compiler (Roslyn) → IL + Metadata → Assembly (.dll/.exe)
→ CLR loads it → JIT compiles IL → Native machine code → CPU
```
**CLR (Common Language Runtime)** provides: garbage collection, exception handling, memory management, type safety, threading.

---

## 2. Program Structure
```csharp
using System;
class Program
{
    static void Main()
    {
        Console.WriteLine("Hello World");
    }
}
```
- `using` → imports a namespace (avoid fully-qualified names).
- `static` → member belongs to the type, not an instance.
- `void` → method returns nothing.
- `namespace` → logically groups related types.

---

## 3. Data Types & Variables

| Type | Purpose | Example |
|---|---|---|
| `int` | whole numbers | `25` |
| `long` | large whole numbers | `9000000000` |
| `float` (4B) | low precision, memory-sensitive | `12.5f` |
| `double` (8B) | general floating point (~15-17 digits) | `12.5` |
| `decimal` (16B) | high precision — **money** (~28-29 digits) | `999.99m` |
| `char` | single character | `'A'` |
| `string` | text (immutable) | `"Raj"` |
| `bool` | true/false | `true` |

**Rule of thumb:** `float` → memory-sensitive · `double` → scientific/general math · `decimal` → financial calculations.

**`var`** — compiler infers the type at compile time; still strongly typed (not dynamic).

**Type conversion:**
```csharp
int age = Convert.ToInt32("22");     // Convert
int age = int.Parse("22");            // Parse — throws on bad input
bool ok = int.TryParse("22", out age); // TryParse — safe for user input
```

**String immutability** — operations return a *new* string, they don't mutate in place.

Useful string methods: `.Length`, `.ToUpper()`, `.ToLower()`, `.Contains()`, `.StartsWith()`, `.EndsWith()`, `.Trim()`, `.Replace()`, `.Substring(start,len)`, interpolation `$"{x}"`.

---

## 4. Operators, Conditionals, Loops

**Operators:** arithmetic `+ - * / %`, comparison `== != > < >= <=`, logical `&& || !`, assignment `+= -= *= /= %=`, increment/decrement `++ --`.
`=` is assignment, `==` is comparison. Integer division truncates: `10/3 == 3`.

**Conditionals:** `if / else if / else`, `switch` (matching one value against many).

**Loops:**
| Loop | When |
|---|---|
| `for` | iteration count generally known |
| `while` | condition checked before each iteration |
| `do-while` | body runs **at least once**, condition checked after |
| `foreach` | iterate a collection (no index needed) |

`break` exits the loop; `continue` skips to the next iteration.

---

## 5. Methods

```csharp
access_modifier return_type MethodName(parameters) { }
```
- **Parameter** (in definition) vs **Argument** (value passed at call site).
- `void` methods can still use bare `return;` to exit early.
- **Method overloading** = same name, different parameter list (return type alone doesn't count).

**Passing values:**
```csharp
void Change(int x) { x = 100; }           // copy — caller unaffected
void Change(ref int x) { x = 100; }       // ref — modifies caller's variable (must be initialized first)
void Get(out int x) { x = 100; }          // out — method must assign it (caller doesn't need to initialize)
```
> `ref` → *I already have a value, modify it.*  `out` → *I don't have one, give me one.*

**Optional parameters & named arguments:**
```csharp
void Greet(string name = "Guest") { }
DisplayEmployee(age: 23, name: "Raj");
```

---

## 6. Classes, Objects, OOP Foundations

- **Class** = blueprint. **Object** = instance (`new ClassName()`).
- **Field** = variable inside a class (internal state).
- **Property** = controlled access to data via `get`/`set`.
```csharp
public double Balance { get; private set; }   // readable outside, only settable inside class
```
- **Constructor** runs on `new`; supports **overloading** (multiple constructors).
- **`this`** disambiguates a field from a same-named parameter (`this.name = name;`).

**Access modifiers:**

| Modifier | Same class | Derived class | Outside |
|---|---|---|---|
| `public` | ✅ | ✅ | ✅ |
| `private` | ✅ | ❌ | ❌ |
| `protected` | ✅ | ✅ | ❌ |
| `internal` | ✅ | within assembly | within assembly |

**Static vs instance:**
- Instance members belong to each object; `static` members belong to the class itself (`ClassName.Member`, no object needed).

---

## 7. The Four OOP Pillars

| Pillar | Meaning |
|---|---|
| **Encapsulation** | protect internal state, expose controlled access |
| **Inheritance** | reuse/extend a base class (`class Dog : Animal`) |
| **Polymorphism** | same abstraction, different behavior |
| **Abstraction** | expose what's needed, hide implementation |

**Polymorphism — two forms:**
```csharp
// Compile-time (overloading)
int Add(int a, int b) {...}
int Add(int a, int b, int c) {...}

// Runtime (overriding)
class Animal { public virtual void Sound() { ... } }
class Dog : Animal { public override void Sound() { ... } }
```
`virtual` in base class allows override; `override` in derived class replaces it.

**Abstraction:**
```csharp
abstract class Animal { public abstract void MakeSound(); }  // cannot instantiate
interface IPaymentService { void Pay(double amount); }        // pure contract
```
> Mental model: **Abstract class → "what are you?"**  **Interface → "what can you do?"**

**Inheritance keywords:**
- `base(...)` calls base constructor or base method (`base.Sound();`).
- `sealed class` cannot be inherited; `sealed override` method cannot be further overridden.
- A class can inherit **one** base class but implement **multiple** interfaces.

---

## 8. Arrays & Collections

| Structure | Key trait |
|---|---|
| `T[]` (Array) | fixed size, `.Length` |
| `List<T>` | dynamic size, `.Count` |
| `Dictionary<TKey,TValue>` | key→value pairs, unique keys |
| `HashSet<T>` | unique values only |

```csharp
int[] nums = {10, 20, 30};                 // index from 0
List<int> list = new List<int>();
list.Add(10); list.Remove(30); list.RemoveAt(0); list.Contains(10); list.Clear();
Dictionary<int,string> d = new(); d.Add(1,"Raj"); d[1];
```
- `Remove(value)` removes by **value**; `RemoveAt(index)` removes by **index**.
- **Generics** (`Box<T>`, `List<T>`) → reusable, type-safe code, less casting.

---

## 9. LINQ & Lambdas

```csharp
var result = numbers.Where(x => x > 20);
var names = users.Where(u => u.Age >= 18).Select(u => u.Name).ToList();
```

| Method | Purpose |
|---|---|
| `Where()` | filter |
| `Select()` | project/transform |
| `First()` / `FirstOrDefault()` | first (or default) match |
| `Single()` | exactly one |
| `Any()` | check existence |
| `Count()` | count |
| `OrderBy()` / `OrderByDescending()` | sort |
| `ToList()` | materialize to `List<T>` |

**Lambda** `x => x > 20` — compact function/expression, heavily used by LINQ.

**`IEnumerable<T>` vs `IQueryable<T>`:** `IEnumerable` enumerates in-memory data; `IQueryable` represents a query that a provider (e.g. ORM/DB) can translate and execute.

---

## 10. Exception Handling

```csharp
try { int x = 10/0; }
catch (FormatException) { }          // specific first
catch (Exception ex) { Console.WriteLine(ex.Message); }  // general last
finally { /* cleanup, always runs */ }

throw new ArgumentException("Invalid age");
```
Common exceptions: `Exception`, `ArgumentException`, `InvalidOperationException`, `NullReferenceException`, `FormatException`, `DivideByZeroException`, `IndexOutOfRangeException`, `OverflowException`.

**Custom exception:**
```csharp
class InvalidAgeException : Exception
{
    public InvalidAgeException(string msg) : base(msg) { }
}
```
Useful members: `ex.Message`, `ex.StackTrace`, `ex.GetType()`.

---

## 11. Nullable Types, Enums, Structs

```csharp
int? age = null;                      // Nullable<int>
if (age.HasValue) Console.WriteLine(age.Value);
int actual = age ?? 0;                // null-coalescing operator
string? name = null;                  // nullable reference type
```

**`enum`** — fixed set of named values (default underlying: `int`, starting at 0):
```csharp
enum EmployeeType { Permanent, Contract, Intern }   // or explicit = 1,2,3
```

**`struct`** — value type (vs `class` = reference type); can hold fields/properties/methods/constructors.

**`readonly`** — assign at declaration or in constructor only, then immutable.

**`static class`** — cannot be instantiated; call members via class name (e.g. `Math.Abs()`).

**Object & collection initializers:**
```csharp
Employee emp = new Employee { Id = 101, Name = "Raj" };
List<string> names = new List<string> { "Raj", "Amit" };
```

---

## 12. Value Types vs Reference Types

- **Value types** (`int`, `double`, `bool`, `struct`, `enum`) — copied on assignment; each variable independent.
- **Reference types** (`class`, `object`, `array`, `delegate`) — variables share the same underlying object; changing via one reference is visible via the other.

---

## 13. Delegates, Events, Custom Events

**Delegate** = type-safe reference to a method (defines a method signature).
```csharp
public delegate void MyDelegate();
MyDelegate d = SayHello;
d();
```
**Built-in delegates:**
```csharp
Action<string> print = msg => Console.WriteLine(msg);   // returns void
Func<int,int,int> add = (a,b) => a + b;                  // last type param = return type
```

**Event** = a notification mechanism built on delegates — lets an object notify subscribers.
```csharp
button.Click += Button_Click;   // subscribe
button.Click -= Button_Click;   // unsubscribe
```
- `sender` = object that raised the event.
- `EventArgs` = carries event data (custom `EventArgs` classes for extra info).

**Building a custom event (5 steps)** — e.g. a Calendar User Control notifying its parent page:
```csharp
// 1. Custom EventArgs
public class CalendarVisibilityChangedEventArgs : EventArgs
{
    public bool IsVisible { get; set; }
    public CalendarVisibilityChangedEventArgs(bool v) { IsVisible = v; }
}
// 2. Delegate (defines handler signature)
public delegate void CalendarVisibilityChangedEventHandler(object sender, CalendarVisibilityChangedEventArgs e);
// 3. Event
public event CalendarVisibilityChangedEventHandler CalendarVisibilityChanged;
// 4. Protected virtual "On<EventName>" raiser method
protected virtual void OnCalendarVisibilityChanged(CalendarVisibilityChangedEventArgs e)
{
    CalendarVisibilityChanged?.Invoke(this, e);   // ?. avoids NullReferenceException if no subscribers
}
// 5. Raise it wherever the state changes
OnCalendarVisibilityChanged(new CalendarVisibilityChangedEventArgs(false));
```
**Publisher/Subscriber:** the User Control *publishes* the event; the parent Web Form *subscribes* — this decouples the reusable control from any specific parent page.

---

## 14. SOLID Principles

| Letter | Principle | Idea |
|---|---|---|
| **S** | Single Responsibility | one class, one reason to change |
| **O** | Open/Closed | open for extension, closed for modification (use interfaces/strategies) |
| **L** | Liskov Substitution | derived types must be usable wherever the base type is expected |
| **I** | Interface Segregation | prefer small, focused interfaces over large ones |
| **D** | Dependency Inversion | depend on abstractions, not concrete implementations |

---

## 15. Dependency Injection (DI)

**Tightly coupled (bad):**
```csharp
class OrderService
{
    private SqlOrderRepository repository;
    public OrderService() { repository = new SqlOrderRepository(); }
}
```
**With DI (good):**
```csharp
class OrderService
{
    private readonly IOrderRepository repository;
    public OrderService(IOrderRepository repository) { this.repository = repository; }
}
```
> **Dependency Inversion** = design principle · **Dependency Injection** = technique to supply dependencies · **DI Container** = tool that auto-wires dependencies.
Constructor injection is the most common form.

---

## 16. Namespaces & Assemblies

- **Namespace** organizes/qualifies types (`namespace MyCompany.Models { ... }`); `using` avoids fully-qualified names.
- **Assembly** = compiled unit of .NET code (`.dll`/`.exe`) containing classes, interfaces, metadata, and dependency references.

---

## 17. Quick Self-Check (things you should be able to explain cold)

- C# vs .NET · Class vs Object · Field vs Property · Constructor
- `public`/`private`/`protected` · `static` vs instance
- Encapsulation, Inheritance, Polymorphism, Abstraction
- Abstract class vs Interface · `virtual`/`override` vs overloading
- `List<T>`, generics, `Dictionary`, LINQ & lambdas
- Exception handling (`try/catch/finally/throw`)
- Value vs reference types · `ref` vs `out`
- Delegates, `Action`/`Func`, custom events (5-step pattern)
- `IEnumerable` vs `IQueryable`
- SOLID · Dependency Inversion vs Dependency Injection
- CLR, namespaces, assemblies
