# C# Language Preparation

## Topics Covered --- 21/08/2026

1.  C# Fundamentals
2.  C# Program Structure
3.  C# Program Execution
4.  Variables and Data Types
5.  Type Handling and Conversion
6.  Operators
7.  Conditional Statements
8.  Loops
9.  Basic String Handling

------------------------------------------------------------------------

# 1. C# Fundamentals

## What is C#?

C# (C-Sharp) is a strongly typed, object-oriented programming language
developed by Microsoft.

It is primarily used to build applications on the .NET platform.

Common uses:

-   Web applications
-   APIs
-   Backend services
-   Desktop applications
-   Cloud applications
-   Games

### C# vs .NET

-   **C#** = programming language
-   **.NET** = development platform, runtime, libraries, and tools

A `.cs` file contains C# source code.

------------------------------------------------------------------------

# 2. C# Program Structure

Basic program:

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

## `using System`

`System` is a namespace provided by .NET.

`using System;` allows us to use types from the `System` namespace
without writing their fully qualified names.

For example:

``` csharp
Console.WriteLine("Hello");
```

instead of:

``` csharp
System.Console.WriteLine("Hello");
```

## Namespace

A namespace logically organizes related types.

``` csharp
namespace MyApplication
{
    class Employee
    {
    }
}
```

## Class

A class is a type/blueprint that can contain members such as:

-   Fields
-   Properties
-   Methods
-   Constructors
-   Events

## `Main()`

For a traditional executable C# application, `Main()` is the entry
point.

``` csharp
static void Main()
{
}
```

## `static`

`static` means the member belongs to the type itself rather than an
instance of that type.

## `void`

`void` means the method does not return a value.

## `Console.WriteLine()`

`Console` is a class and `WriteLine()` is a method used to write output
to the console.

------------------------------------------------------------------------

# 3. C# Program Execution

The basic execution pipeline is:

``` text
C# Source Code
      ↓
C# Compiler (Roslyn)
      ↓
IL + Metadata
      ↓
Assembly (.dll/.exe)
      ↓
CLR / .NET Runtime
      ↓
JIT Compiler
      ↓
Native Machine Code
      ↓
CPU
```

## Step 1 --- Source Code

You write C# code in `.cs` files.

## Step 2 --- Compilation

The C# compiler compiles the source code into Intermediate Language (IL)
and metadata.

## Step 3 --- Assembly

The compiled output is packaged into an assembly, commonly a `.dll` or
`.exe`.

## Step 4 --- Runtime

The .NET runtime/CLR loads the assembly and provides runtime services.

## Step 5 --- JIT

The Just-In-Time compiler converts required IL into native machine code
that the CPU can execute.

## Runtime services include

-   JIT compilation
-   Garbage collection
-   Exception handling
-   Type safety
-   Threading support
-   Memory management

------------------------------------------------------------------------

# 4. Variables and Data Types

A variable is a named storage location used to hold a value.

``` csharp
int age = 25;
```

Breakdown:

``` text
int
 ↓
data type

age
 ↓
variable name

=
 ↓
assignment

25
 ↓
value
```

## Important Data Types

  Type        Purpose                               Example
  ----------- ------------------------------------- --------------
  `int`       Whole numbers                         `25`
  `long`      Large whole numbers                   `9000000000`
  `float`     Floating-point values                 `12.5f`
  `double`    General floating-point calculations   `12.5`
  `decimal`   High-precision decimal values         `999.99m`
  `char`      Single character                      `'A'`
  `string`    Text                                  `"Raj"`
  `bool`      True/false                            `true`

## `float` vs `double` vs `decimal`

### `float`

-   4 bytes
-   Lower precision
-   Useful when memory usage matters and high precision is not required
-   Decimal literal usually requires `f`

``` csharp
float temperature = 36.5f;
```

### `double`

-   8 bytes
-   About 15--17 significant decimal digits of precision
-   General-purpose floating-point type

``` csharp
double distance = 25.75;
```

### `decimal`

-   16 bytes
-   About 28--29 significant decimal digits of precision
-   Designed for high-precision decimal arithmetic
-   Commonly used for money and financial calculations

``` csharp
decimal price = 999.99m;
```

### Rule of thumb

``` text
float   → lower precision / memory-sensitive floating-point work
double  → general mathematical/scientific calculations
decimal → financial/monetary calculations
```

------------------------------------------------------------------------

# 5. `var`

`var` means the compiler infers the variable's compile-time type from
the assigned value.

``` csharp
var name = "Raj";
```

The compiler infers:

``` text
name → string
```

Similarly:

``` csharp
var age = 22;
```

means:

``` text
age → int
```

`var` is **not dynamically typed**.

This is invalid:

``` csharp
var age = 22;
age = "Raj";
```

because `age` was inferred as `int`.

------------------------------------------------------------------------

# 6. Type Handling and Conversion

Web applications frequently receive input as strings, so type conversion
is important.

## `Convert`

``` csharp
string ageText = "22";

int age = Convert.ToInt32(ageText);
```

## `Parse`

``` csharp
int age = int.Parse("22");
```

`Parse()` throws an exception if the input cannot be parsed.

## `TryParse`

`TryParse()` is safer for user input.

``` csharp
string input = "22";

int age;

if (int.TryParse(input, out age))
{
    Console.WriteLine(age);
}
else
{
    Console.WriteLine("Invalid number");
}
```

------------------------------------------------------------------------

# 7. Operators

## Arithmetic

``` text
+   Addition
-   Subtraction
*   Multiplication
/   Division
%   Remainder
```

Example:

``` csharp
int a = 10;
int b = 3;

Console.WriteLine(a + b); // 13
Console.WriteLine(a - b); // 7
Console.WriteLine(a * b); // 30
Console.WriteLine(a / b); // 3
Console.WriteLine(a % b); // 1
```

### Integer division

``` csharp
int result = 10 / 3;
```

Result:

``` text
3
```

because both operands are integers.

------------------------------------------------------------------------

## Comparison

``` text
==   Equal
!=   Not equal
>    Greater than
<    Less than
>=   Greater than or equal
<=   Less than or equal
```

Comparison expressions return `bool`.

``` csharp
age >= 18
```

produces either `true` or `false`.

### `=` vs `==`

``` text
=   → assignment
==  → comparison
```

------------------------------------------------------------------------

## Logical Operators

### AND `&&`

Both conditions must be true.

``` csharp
age >= 18 && isActive
```

### OR `||`

At least one condition must be true.

``` csharp
age >= 18 || hasPermission
```

### NOT `!`

Reverses a Boolean value.

``` csharp
!isActive
```

------------------------------------------------------------------------

## Assignment Operators

``` text
+=
-=
*=
/=
%=
```

Example:

``` csharp
int score = 10;

score += 5; // 15
score *= 2; // 30
```

------------------------------------------------------------------------

## Increment / Decrement

``` text
++  → increase by 1
--  → decrease by 1
```

``` csharp
int count = 10;

count++;
```

`count` becomes `11`.

------------------------------------------------------------------------

# 8. Conditional Statements

## `if`

``` csharp
int age = 25;

if (age >= 18)
{
    Console.WriteLine("Adult");
}
```

The condition must evaluate to `true` or `false`.

## `if / else`

``` csharp
int age = 15;

if (age >= 18)
{
    Console.WriteLine("Adult");
}
else
{
    Console.WriteLine("Minor");
}
```

## `else if`

``` csharp
int marks = 85;

if (marks >= 90)
{
    Console.WriteLine("A+");
}
else if (marks >= 80)
{
    Console.WriteLine("A");
}
else if (marks >= 70)
{
    Console.WriteLine("B");
}
else
{
    Console.WriteLine("C");
}
```

Conditions are evaluated from top to bottom.

## `switch`

Use `switch` when matching a value against multiple alternatives.

``` csharp
string empType = "Permanent";

switch (empType)
{
    case "Permanent":
        Console.WriteLine("Permanent Employee");
        break;

    case "Contract":
        Console.WriteLine("Contract Employee");
        break;

    case "Intern":
        Console.WriteLine("Intern");
        break;

    default:
        Console.WriteLine("Unknown Employee Type");
        break;
}
```

------------------------------------------------------------------------

# 9. Loops

Loops execute code repeatedly.

The four basic loops are:

``` text
for
while
do-while
foreach
```

## `for`

Use when the number of iterations is generally known.

``` csharp
for (int i = 0; i < 5; i++)
{
    Console.WriteLine(i);
}
```

Output:

``` text
0
1
2
3
4
```

Structure:

``` text
initialization
      ↓
condition
      ↓
body
      ↓
increment
      ↓
condition again
```

## `while`

The condition is checked before each iteration.

``` csharp
int i = 0;

while (i < 5)
{
    Console.WriteLine(i);
    i++;
}
```

## `do-while`

The body executes at least once because the condition is checked
afterward.

``` csharp
int i = 10;

do
{
    Console.WriteLine(i);
    i++;
}
while (i < 5);
```

Output:

``` text
10
```

### Difference

``` text
while
→ condition first

do-while
→ body first, condition afterward
```

## `foreach`

Used to iterate through a collection.

``` csharp
List<string> names = new List<string>
{
    "Raj",
    "Amit",
    "Vishal"
};

foreach (string name in names)
{
    Console.WriteLine(name);
}
```

Use `for` when you need index-based control.

Use `foreach` when you simply need each item.

## `break`

Immediately exits the loop.

``` csharp
for (int i = 1; i <= 10; i++)
{
    if (i == 5)
    {
        break;
    }

    Console.WriteLine(i);
}
```

Output:

``` text
1
2
3
4
```

## `continue`

Skips the current iteration and proceeds to the next iteration.

``` csharp
for (int i = 1; i <= 5; i++)
{
    if (i == 3)
    {
        continue;
    }

    Console.WriteLine(i);
}
```

Output:

``` text
1
2
4
5
```

------------------------------------------------------------------------

# 10. Basic String Handling

## Creating a string

``` csharp
string name = "Raj";
```

## `Length`

``` csharp
string name = "Raj";

Console.WriteLine(name.Length);
```

Output:

``` text
3
```

## `ToUpper()`

``` csharp
name.ToUpper();
```

Returns the uppercase version.

## `ToLower()`

``` csharp
name.ToLower();
```

Returns the lowercase version.

## `Contains()`

``` csharp
string name = "Raj Pohekar";

bool result = name.Contains("Raj");
```

Returns `true` or `false`.

## `StartsWith()`

``` csharp
name.StartsWith("R");
```

## `EndsWith()`

``` csharp
name.EndsWith("r");
```

## `Trim()`

Removes leading and trailing whitespace.

``` csharp
string name = "  Raj  ";

name = name.Trim();
```

Result:

``` text
Raj
```

It does not remove spaces inside the string.

## `Replace()`

``` csharp
string message = "Hello Raj";

message = message.Replace("Raj", "Amit");
```

Result:

``` text
Hello Amit
```

## `Substring()`

Indexes start from zero.

``` csharp
string text = "Hello World";

string result = text.Substring(0, 5);
```

Result:

``` text
Hello
```

Syntax:

``` text
Substring(startIndex, length)
```

## Concatenation

``` csharp
string firstName = "Raj";
string lastName = "Pohekar";

string fullName = firstName + " " + lastName;
```

## String interpolation

``` csharp
string firstName = "Raj";
int age = 22;

string message = $"My name is {firstName} and I am {age}.";
```

## String immutability

Strings are immutable in C#.

String operations produce a new string value rather than modifying the
existing string object in place.

------------------------------------------------------------------------

# Quick Revision Checklist

-   [ ] Explain C# vs .NET
-   [ ] Explain namespace and `using`
-   [ ] Explain class and `Main()`
-   [ ] Explain C# compilation and execution
-   [ ] Know common data types
-   [ ] Explain `float`, `double`, and `decimal`
-   [ ] Explain `var`
-   [ ] Perform type conversions
-   [ ] Know all basic operators
-   [ ] Write `if/else` and `switch`
-   [ ] Write `for`, `while`, `do-while`, and `foreach`
-   [ ] Explain `break` vs `continue`
-   [ ] Use common string methods
