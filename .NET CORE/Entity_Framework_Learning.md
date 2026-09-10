# Entity Framework / Entity Framework Core — Documentation

## 1. What is Entity Framework?

**Entity Framework (EF)** is an **Object-Relational Mapper (ORM)** for .NET.

An ORM allows an application to work with a relational database using **C# objects, classes, and LINQ** instead of manually writing SQL for every database operation.

### Without Entity Framework

```csharp
string query = "SELECT * FROM Employees WHERE EmpID = @EmpID";
SqlCommand cmd = new SqlCommand(query, connection);
```

### With Entity Framework

```csharp
var employee = context.Employees.Find(empId);
```

Entity Framework generates and executes the required SQL behind the scenes.

---

# 2. What is ORM?

**ORM = Object-Relational Mapping**

It maps:

| Database | C# |
|---|---|
| Table | Class |
| Row | Object |
| Column | Property |
| Primary Key | Key Property |
| Relationship | Navigation Property |

Example database table:

```text
Employees
--------------------------------
EmpID       INT
EmpName     VARCHAR
EmpType     VARCHAR
DOJ         DATE
```

Corresponding C# class:

```csharp
public class Employee
{
    public int EmpID { get; set; }
    public string EmpName { get; set; }
    public string EmpType { get; set; }
    public DateTime DOJ { get; set; }
}
```

---

# 3. Entity Framework vs Entity Framework Core

There are two important versions.

## Entity Framework 6

Usually called:

```text
Entity Framework 6
EF6
```

It is primarily associated with the older **.NET Framework** ecosystem.

Example:

```text
ASP.NET Web Forms
ASP.NET MVC 5
.NET Framework
```

## Entity Framework Core

Usually called:

```text
EF Core
```

It is the modern version designed for modern .NET.

Example:

```text
.NET 6
.NET 7
.NET 8
.NET 9
.NET 10
ASP.NET Core
```

For new applications, **EF Core is generally the technology to learn**.

---

# 4. Why use Entity Framework?

Without an ORM, application code often contains a lot of:

- SQL queries
- `SqlConnection`
- `SqlCommand`
- `SqlDataReader`
- Manual object mapping
- Parameter handling

Entity Framework provides abstractions for these common operations.

### Benefits

- Less repetitive database code
- Strongly typed C# code
- LINQ support
- Automatic object mapping
- Change tracking
- Relationship handling
- Transactions
- Migrations
- Asynchronous database operations
- Integration with ASP.NET Core dependency injection

---

# 5. Basic Architecture

A simplified architecture looks like this:

```text
┌───────────────────────┐
│   ASP.NET Core App    │
└───────────┬───────────┘
            │
            ↓
┌───────────────────────┐
│     Application       │
│   / Service Layer     │
└───────────┬───────────┘
            │
            ↓
┌───────────────────────┐
│      DbContext        │
└───────────┬───────────┘
            │
            ↓
┌───────────────────────┐
│      EF Core          │
│  Change Tracking      │
│  LINQ Translation     │
│  Query Generation     │
└───────────┬───────────┘
            │
            ↓
┌───────────────────────┐
│   Database Provider   │
│      SQL Server       │
└───────────┬───────────┘
            │
            ↓
┌───────────────────────┐
│       Database        │
└───────────────────────┘
```

---

# 6. Important Entity Framework Concepts

The most important concepts to understand are:

1. Entity
2. Entity Class
3. `DbContext`
4. `DbSet`
5. Connection String
6. LINQ
7. Change Tracking
8. CRUD
9. Relationships
10. Data Annotations
11. Fluent API
12. Migrations
13. Database First
14. Code First
15. Transactions
16. Eager Loading
17. Explicit Loading
18. Lazy Loading
19. Asynchronous Queries
20. Dependency Injection

---

# 7. Entity

An **entity** is an object that represents data stored in the database.

Example:

```csharp
public class Employee
{
    public int EmpID { get; set; }
    public string EmpName { get; set; }
    public string EmpType { get; set; }
}
```

Here:

```text
Employee
   ↓
Database Employee table
```

An individual object represents one row:

```csharp
Employee employee = new Employee
{
    EmpID = 1,
    EmpName = "Raj",
    EmpType = "Permanent"
};
```

---

# 8. DbContext

`DbContext` is one of the most important classes in EF Core.

It represents a **session with the database** and provides:

- Database access
- Entity tracking
- Query execution
- Saving changes
- Transaction coordination

Example:

```csharp
public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(
        DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    public DbSet<Employee> Employees { get; set; }
}
```

Think of `DbContext` as the main gateway between your C# application and the database.

```text
Application
     ↓
DbContext
     ↓
Database
```

---

# 9. DbSet

`DbSet<TEntity>` represents a collection of entities of a particular type.

Example:

```csharp
public DbSet<Employee> Employees { get; set; }
```

This usually represents:

```text
Employees table
```

You can query it:

```csharp
var employees = context.Employees.ToList();
```

You can add records:

```csharp
context.Employees.Add(employee);
```

You can remove records:

```csharp
context.Employees.Remove(employee);
```

---

# 10. Connection String

A connection string tells EF Core how to connect to the database.

Example for SQL Server:

```json
{
  "ConnectionStrings": {
    "DefaultConnection":
      "Server=localhost;Database=EmployeeDB;Trusted_Connection=True;TrustServerCertificate=True"
  }
}
```

The application reads this configuration and passes it to EF Core.

---

# 11. Registering DbContext

In ASP.NET Core:

```csharp
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection")));
```

This also registers the context with **Dependency Injection**.

---

# 12. Dependency Injection

Instead of manually creating a `DbContext`:

```csharp
var context = new ApplicationDbContext(...);
```

ASP.NET Core can inject it into a class.

Example:

```csharp
public class EmployeeService
{
    private readonly ApplicationDbContext _context;

    public EmployeeService(ApplicationDbContext context)
    {
        _context = context;
    }
}
```

This is the preferred approach in ASP.NET Core applications.

---

# 13. CRUD Operations

CRUD means:

```text
C → Create
R → Read
U → Update
D → Delete
```

---

## 13.1 Create

```csharp
var employee = new Employee
{
    EmpName = "Raj",
    EmpType = "Permanent"
};

context.Employees.Add(employee);

context.SaveChanges();
```

`SaveChanges()` sends the required SQL operation to the database.

Conceptually:

```sql
INSERT INTO Employees (...)
VALUES (...);
```

---

# 14. Read

Get all employees:

```csharp
var employees = context.Employees.ToList();
```

Find by primary key:

```csharp
var employee = context.Employees.Find(1);
```

Filter:

```csharp
var employees = context.Employees
                       .Where(e => e.EmpType == "Permanent")
                       .ToList();
```

---

# 15. Update

```csharp
var employee = context.Employees.Find(1);

if (employee != null)
{
    employee.EmpName = "Updated Name";

    context.SaveChanges();
}
```

EF Core detects that the entity changed.

This is called:

**Change Tracking**

---

# 16. Delete

```csharp
var employee = context.Employees.Find(1);

if (employee != null)
{
    context.Employees.Remove(employee);

    context.SaveChanges();
}
```

Conceptually:

```sql
DELETE FROM Employees
WHERE EmpID = 1;
```

---

# 17. LINQ

One of the biggest advantages of EF Core is LINQ.

LINQ allows you to query entities using C# syntax.

Example:

```csharp
var employees = context.Employees
                       .Where(e => e.EmpType == "Permanent")
                       .ToList();
```

EF Core translates the LINQ expression into SQL.

Conceptually:

```text
C# LINQ
   ↓
EF Core
   ↓
SQL
   ↓
Database
```

---

# 18. Common LINQ Operations

## Where

Filtering:

```csharp
var employees = context.Employees
                       .Where(e => e.EmpType == "Intern")
                       .ToList();
```

## FirstOrDefault

```csharp
var employee = context.Employees
                      .FirstOrDefault(e => e.EmpID == 10);
```

## Any

Check whether records exist:

```csharp
bool exists = context.Employees
                     .Any(e => e.EmpName == "Raj");
```

## Count

```csharp
int count = context.Employees.Count();
```

## OrderBy

```csharp
var employees = context.Employees
                       .OrderBy(e => e.EmpName)
                       .ToList();
```

## Select

Select specific fields:

```csharp
var employees = context.Employees
                       .Select(e => new
                       {
                           e.EmpID,
                           e.EmpName
                       })
                       .ToList();
```

---

# 19. IQueryable vs IEnumerable

This is important for understanding EF Core performance.

## IQueryable

The query can be translated to SQL and executed by the database.

```csharp
var employees = context.Employees
                       .Where(e => e.EmpType == "Permanent");
```

At this point, the query has not necessarily executed yet.

## ToList()

```csharp
var employees = context.Employees
                       .Where(e => e.EmpType == "Permanent")
                       .ToList();
```

`ToList()` causes the query to execute.

Conceptually:

```text
IQueryable
    ↓
Build SQL
    ↓
Execute SQL
    ↓
Database
```

---

# 20. Deferred Execution

LINQ queries against EF Core are generally executed when the results are actually requested.

Example:

```csharp
var query = context.Employees
                   .Where(e => e.EmpType == "Permanent");
```

The database query has not necessarily executed yet.

When:

```csharp
var employees = query.ToList();
```

the query executes.

Other operations that can trigger execution include:

```csharp
First()
FirstOrDefault()
Single()
Count()
Any()
ToList()
ToArray()
```

---

# 21. Change Tracking

EF Core tracks entity objects retrieved from the database.

Example:

```csharp
var employee = context.Employees.Find(1);

employee.EmpName = "Raj";
```

EF Core knows that:

```text
Original:
EmpName = "Old Name"

Current:
EmpName = "Raj"
```

When:

```csharp
context.SaveChanges();
```

EF Core generates the required update.

---

# 22. Entity States

EF Core commonly tracks entities using states such as:

```text
Added
Unchanged
Modified
Deleted
Detached
```

Example:

```csharp
context.Employees.Add(employee);
```

The entity becomes:

```text
Added
```

After `SaveChanges()`:

```text
Unchanged
```

If you modify it:

```csharp
employee.EmpName = "New Name";
```

it becomes:

```text
Modified
```

---

# 23. SaveChanges()

`SaveChanges()` persists tracked changes to the database.

Example:

```csharp
context.Employees.Add(employee);

context.SaveChanges();
```

Without `SaveChanges()`, simply calling:

```csharp
Add()
Remove()
```

does not normally persist the change to the database.

---

# 24. Primary Keys

EF Core needs to know which property represents the primary key.

Convention example:

```csharp
public int EmployeeId { get; set; }
```

EF Core recognizes common key naming conventions.

You can explicitly specify it:

```csharp
using System.ComponentModel.DataAnnotations;

public class Employee
{
    [Key]
    public int EmpID { get; set; }

    public string EmpName { get; set; }
}
```

---

# 25. Data Annotations

Data Annotations allow you to configure entities using attributes.

Example:

```csharp
using System.ComponentModel.DataAnnotations;

public class Employee
{
    [Key]
    public int EmpID { get; set; }

    [Required]
    [MaxLength(100)]
    public string EmpName { get; set; }

    [MaxLength(30)]
    public string EmpType { get; set; }
}
```

Common annotations include:

```text
[Key]
[Required]
[MaxLength]
[MinLength]
[StringLength]
[DatabaseGenerated]
[ForeignKey]
[Table]
[Column]
```

---

# 26. Fluent API

Fluent API allows database configuration inside `OnModelCreating()`.

Example:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Employee>()
        .HasKey(e => e.EmpID);

    modelBuilder.Entity<Employee>()
        .Property(e => e.EmpName)
        .HasMaxLength(100)
        .IsRequired();
}
```

Fluent API is useful for complex configurations and relationships.

---

# 27. Data Annotations vs Fluent API

| Data Annotations | Fluent API |
|---|---|
| Attributes on classes | Configuration in `OnModelCreating()` |
| Easy to understand | More powerful |
| Good for simple rules | Better for complex rules |
| Less configuration code | More explicit configuration |

For larger applications, Fluent API is often preferred for complex database mappings.

---

# 28. Relationships

Relational databases commonly have:

```text
One-to-One
One-to-Many
Many-to-Many
```

---

# 29. One-to-Many Relationship

Example:

```text
Department
    |
    | 1
    |
    | *
Employees
```

One department can have many employees.

C#:

```csharp
public class Department
{
    public int DepartmentId { get; set; }

    public string Name { get; set; }

    public ICollection<Employee> Employees { get; set; }
}
```

Employee:

```csharp
public class Employee
{
    public int EmployeeId { get; set; }

    public string Name { get; set; }

    public int DepartmentId { get; set; }

    public Department Department { get; set; }
}
```

Here:

```text
DepartmentId
```

is the foreign key.

---

# 30. Navigation Properties

A navigation property represents a relationship between entities.

Example:

```csharp
public Department Department { get; set; }
```

This allows you to navigate:

```text
Employee → Department
```

Collection navigation:

```csharp
public ICollection<Employee> Employees { get; set; }
```

allows:

```text
Department → Employees
```

---

# 31. Loading Related Data

EF Core provides three major approaches:

1. Eager Loading
2. Explicit Loading
3. Lazy Loading

---

## 31.1 Eager Loading

Load related data as part of the query.

```csharp
var employees = context.Employees
                       .Include(e => e.Department)
                       .ToList();
```

This is commonly used and explicit.

---

## 31.2 Explicit Loading

Load related data manually.

```csharp
var employee = context.Employees.Find(1);

context.Entry(employee)
       .Reference(e => e.Department)
       .Load();
```

---

## 31.3 Lazy Loading

Related data is loaded automatically when accessed, when lazy-loading proxies are configured.

Example:

```csharp
var employee = context.Employees.First();

var department = employee.Department;
```

Lazy loading can be convenient but can also cause unexpected database queries and **N+1 query problems**.

Use it carefully.

---

# 32. Include and ThenInclude

For related data:

```csharp
var employees = context.Employees
                       .Include(e => e.Department)
                       .ToList();
```

For deeper relationships:

```csharp
var employees = context.Employees
                       .Include(e => e.Department)
                       .ThenInclude(d => d.Manager)
                       .ToList();
```

---

# 33. Code First

In **Code First**, you define your C# classes first.

Example:

```text
C# Classes
    ↓
EF Core
    ↓
Migrations
    ↓
Database
```

Example:

```csharp
public class Employee
{
    public int EmployeeId { get; set; }
    public string Name { get; set; }
}
```

EF Core can create/update the database schema through migrations.

---

# 34. Database First

In **Database First**, the database already exists.

```text
Existing Database
       ↓
EF Core Scaffolding
       ↓
C# Entity Classes
       +
DbContext
```

This is useful when working with an existing enterprise database.

---

# 35. Migrations

Migrations allow EF Core to track and apply database schema changes.

Suppose you initially have:

```csharp
public class Employee
{
    public int EmployeeId { get; set; }
    public string Name { get; set; }
}
```

Later you add:

```csharp
public string Email { get; set; }
```

You can create a migration.

Typical CLI commands:

```bash
dotnet ef migrations add AddEmployeeEmail
```

Then:

```bash
dotnet ef database update
```

Conceptually:

```text
Model Change
     ↓
Migration
     ↓
SQL Schema Change
     ↓
Database
```

---

# 36. EF Core CLI

Common commands:

```bash
dotnet ef migrations add InitialCreate
```

Create a migration.

```bash
dotnet ef database update
```

Apply migrations.

```bash
dotnet ef migrations remove
```

Remove the last migration when appropriate.

For existing databases, scaffolding can be used to generate models and a context.

---

# 37. Transactions

A transaction ensures that multiple database operations succeed or fail together.

Example:

```csharp
using var transaction =
    await context.Database.BeginTransactionAsync();

try
{
    // Operation 1

    // Operation 2

    await context.SaveChangesAsync();

    await transaction.CommitAsync();
}
catch
{
    await transaction.RollbackAsync();
    throw;
}
```

Concept:

```text
Operation A
    +
Operation B
    +
Operation C
    ↓
Commit
```

If something fails:

```text
Rollback
```

---

# 38. Async Operations

ASP.NET Core applications commonly use asynchronous EF Core operations.

Instead of:

```csharp
var employees = context.Employees.ToList();
```

use:

```csharp
var employees = await context.Employees.ToListAsync();
```

Other common async methods:

```csharp
FirstOrDefaultAsync()
SingleOrDefaultAsync()
AnyAsync()
CountAsync()
SaveChangesAsync()
FindAsync()
```

Example:

```csharp
public async Task<List<Employee>> GetEmployees()
{
    return await context.Employees.ToListAsync();
}
```

Async database operations help avoid blocking application threads while waiting for I/O.

---

# 39. Tracking vs No Tracking

By default, EF Core tracks entities returned from normal queries.

For read-only operations, use:

```csharp
var employees = await context.Employees
                             .AsNoTracking()
                             .ToListAsync();
```

`AsNoTracking()` tells EF Core not to track the returned entities.

Useful when:

- Data is read-only
- You do not need to update the entities
- You want to reduce tracking overhead

---

# 40. Projection

Instead of loading an entire entity:

```csharp
var employees = await context.Employees
                             .ToListAsync();
```

you can select only the required fields:

```csharp
var employees = await context.Employees
    .Select(e => new
    {
        e.EmpID,
        e.EmpName
    })
    .ToListAsync();
```

This can reduce unnecessary data retrieval.

---

# 41. Raw SQL

EF Core supports raw SQL when required.

Example:

```csharp
var employees = await context.Employees
    .FromSqlRaw("SELECT * FROM Employees")
    .ToListAsync();
```

For parameterized values, prefer APIs that safely parameterize input.

Do **not** construct SQL by concatenating user input.

Bad:

```csharp
var sql = "SELECT * FROM Employees WHERE Name = '" + name + "'";
```

This can lead to SQL injection.

---

# 42. Stored Procedures

Enterprise applications may already use stored procedures.

EF Core can work alongside stored procedures and raw SQL when necessary.

You should understand both:

```text
EF Core LINQ
```

and:

```text
Stored Procedures / Raw SQL
```

because real-world enterprise applications often use a mixture of approaches.

---

# 43. EF Core vs ADO.NET

| ADO.NET | EF Core |
|---|---|
| Lower-level database access | ORM |
| Write SQL manually | LINQ can generate SQL |
| Manual mapping | Automatic object mapping |
| More boilerplate | Less boilerplate |
| Fine-grained SQL control | Higher productivity |
| `SqlConnection` | `DbContext` |
| `SqlCommand` | LINQ / EF APIs |
| `SqlDataReader` | Entities / projections |

ADO.NET is not obsolete. EF Core is an abstraction built for higher-level data access.

---

# 44. EF Core vs Dapper

Dapper is a lightweight micro-ORM.

| EF Core | Dapper |
|---|---|
| Full ORM | Micro-ORM |
| Change tracking | Minimal/no built-in change tracking |
| LINQ | SQL-focused |
| Relationships supported through ORM features | More manual |
| Migrations available | No EF-style migrations |
| More abstraction | Less abstraction |
| More features | Simpler and lightweight |

Choice depends on the application's requirements.

---

# 45. Typical ASP.NET Core Architecture

A common structure might look like:

```text
MyApplication
│
├── Controllers
│
├── Services
│
├── Repositories
│
├── Models
│   └── Employee.cs
│
├── Data
│   └── ApplicationDbContext.cs
│
├── DTOs
│
├── Migrations
│
├── appsettings.json
│
└── Program.cs
```

A request might flow like:

```text
HTTP Request
     ↓
Controller
     ↓
Service
     ↓
Repository / DbContext
     ↓
EF Core
     ↓
SQL Server
```

Note that a repository layer is **not mandatory** in every EF Core application. `DbContext` already provides many repository/unit-of-work-like capabilities.

---

# 46. Example: Employee Application

Suppose the database contains:

```text
Employee
--------------------------------
EmpID
EmpName
EmpType
FilePath
DOJ
IsDeleted
CreatedDate
ModifiedDate
```

Entity:

```csharp
public class Employee
{
    public int EmpID { get; set; }

    public string EmpName { get; set; }

    public string EmpType { get; set; }

    public string FilePath { get; set; }

    public DateTime DOJ { get; set; }

    public bool IsDeleted { get; set; }

    public DateTime CreatedDate { get; set; }

    public DateTime? ModifiedDate { get; set; }
}
```

Context:

```csharp
public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(
        DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    public DbSet<Employee> Employees { get; set; }
}
```

Query:

```csharp
var employees = await context.Employees
    .Where(e => !e.IsDeleted)
    .OrderBy(e => e.EmpName)
    .ToListAsync();
```

---

# 47. Soft Delete

Many enterprise systems do not physically delete records.

Instead of:

```sql
DELETE FROM Employees
WHERE EmpID = 10;
```

they may use:

```text
IsDeleted = true
```

EF Core query:

```csharp
var employees = await context.Employees
    .Where(e => !e.IsDeleted)
    .ToListAsync();
```

EF Core also supports **global query filters** for this pattern.

Example:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Employee>()
        .HasQueryFilter(e => !e.IsDeleted);
}
```

Now normal queries automatically exclude deleted records.

---

# 48. Concurrency

Concurrency becomes important when multiple users modify the same record.

Example:

```text
User A reads Employee
User B reads Employee

User A updates Employee
User B updates Employee
```

Without concurrency handling, one update may overwrite another.

EF Core supports optimistic concurrency using concurrency tokens.

Example:

```csharp
[Timestamp]
public byte[] RowVersion { get; set; }
```

The exact implementation depends on the database provider.

---

# 49. Common Performance Practices

## Use projections

```csharp
.Select(e => new
{
    e.EmpID,
    e.EmpName
})
```

## Use AsNoTracking for read-only queries

```csharp
.AsNoTracking()
```

## Avoid unnecessary `ToList()`

Bad:

```csharp
var employees = context.Employees.ToList();

var permanent = employees
    .Where(e => e.EmpType == "Permanent")
    .ToList();
```

Better:

```csharp
var permanent = await context.Employees
    .Where(e => e.EmpType == "Permanent")
    .ToListAsync();
```

The filtering can happen in the database.

## Avoid N+1 queries

Be careful with repeatedly querying related data inside loops.

Prefer appropriate eager loading or projection.

---

# 50. SQL Is Still Important

Learning EF Core does **not** mean you can ignore SQL.

You should understand:

- SELECT
- INSERT
- UPDATE
- DELETE
- JOIN
- GROUP BY
- ORDER BY
- WHERE
- Indexes
- Primary keys
- Foreign keys
- Transactions
- Stored procedures
- Query execution
- Query performance

Why?

Because EF Core ultimately interacts with a relational database.

You should know what SQL your LINQ query is likely to generate.

---

# 51. How EF Core Executes a Query

Example:

```csharp
var employees = await context.Employees
    .Where(e => e.EmpType == "Permanent")
    .ToListAsync();
```

Conceptually:

```text
1. C# LINQ expression
          ↓
2. EF Core query translation
          ↓
3. SQL generated
          ↓
4. SQL sent to database
          ↓
5. Database executes query
          ↓
6. Rows returned
          ↓
7. EF Core creates Employee objects
          ↓
8. Application receives objects
```

This is one of the most important concepts to understand.

---

# 52. Important Classes and Interfaces

Know these:

```text
DbContext
DbSet<TEntity>
DbContextOptions
ModelBuilder
EntityEntry
ChangeTracker
```

Useful interfaces/types:

```text
IQueryable<T>
IEnumerable<T>
IAsyncEnumerable<T>
```

---

# 53. Common Mistakes

## Mistake 1: Loading the entire table

```csharp
var employees = context.Employees.ToList();
```

when only a few records are required.

Prefer filtering:

```csharp
var employees = context.Employees
    .Where(e => !e.IsDeleted)
    .ToList();
```

## Mistake 2: Ignoring generated SQL

A simple-looking LINQ query can sometimes generate an expensive SQL query.

Always consider:

```text
What SQL will this generate?
```

## Mistake 3: N+1 queries

Repeatedly querying the database inside a loop can be expensive.

## Mistake 4: Using tracked entities for read-only operations

Consider:

```csharp
AsNoTracking()
```

for read-only queries.

## Mistake 5: SQL injection with raw SQL

Never concatenate untrusted input into SQL.

---

# 54. Important Interview Questions

You should be able to answer:

### Fundamentals

- What is Entity Framework?
- What is an ORM?
- What is EF Core?
- EF6 vs EF Core?
- Why use EF Core?
- What is `DbContext`?
- What is `DbSet`?

### Querying

- What is LINQ?
- What is `IQueryable`?
- What is `IEnumerable`?
- What is deferred execution?
- When does an EF Core query execute?
- What does `ToList()` do?

### Tracking

- What is change tracking?
- What are entity states?
- What is `AsNoTracking()`?
- Why is change tracking useful?

### Database

- What is Code First?
- What is Database First?
- What are migrations?
- What is scaffolding?
- How are relationships configured?

### Relationships

- What is a navigation property?
- What is a foreign key?
- What is eager loading?
- What is lazy loading?
- What is explicit loading?
- What is `Include()`?

### Performance

- What is the N+1 problem?
- Why use projection?
- When should you use `AsNoTracking()`?
- How can you inspect generated SQL?
- How do indexes affect EF Core queries?

### Production

- How are transactions handled?
- How is concurrency handled?
- How should connection strings be configured?
- How should `DbContext` lifetime be managed?
- When should raw SQL or stored procedures be used?

---

# 55. Minimum Knowledge Required for ASP.NET Core Development

Before working seriously with EF Core, understand:

```text
C#
│
├── Classes / Objects
├── OOP
├── Interfaces
├── Generics
├── Exceptions
├── Async / Await
├── LINQ
└── Collections

.NET
│
├── Dependency Injection
├── Configuration
├── Logging
└── Application Lifecycle

SQL
│
├── CRUD
├── JOINs
├── Keys
├── Relationships
├── Indexes
└── Transactions

EF Core
│
├── Entity
├── DbContext
├── DbSet
├── LINQ
├── CRUD
├── Change Tracking
├── Relationships
├── Migrations
├── Loading
├── Transactions
└── Performance
```

---

# 56. Mental Model

The simplest way to remember EF Core is:

```text
C# Class
    ↕
Entity
    ↕
DbSet
    ↕
DbContext
    ↕
EF Core
    ↕
SQL
    ↕
Database
```

For example:

```csharp
var employee = await context.Employees
    .FirstOrDefaultAsync(e => e.EmpID == 1);
```

Think:

```text
C# LINQ
   ↓
EF Core
   ↓
SQL query
   ↓
SQL Server
   ↓
Database row
   ↓
Employee object
```

---

# 57. EF Core Learning Order

A practical order for learning is:

```text
1. ORM concept
        ↓
2. EF Core architecture
        ↓
3. Entity classes
        ↓
4. DbContext
        ↓
5. DbSet
        ↓
6. Connection configuration
        ↓
7. CRUD
        ↓
8. LINQ
        ↓
9. Change Tracking
        ↓
10. Relationships
        ↓
11. Include / ThenInclude
        ↓
12. Data Annotations
        ↓
13. Fluent API
        ↓
14. Code First
        ↓
15. Migrations
        ↓
16. Database First / Scaffolding
        ↓
17. Async queries
        ↓
18. Transactions
        ↓
19. Concurrency
        ↓
20. Performance optimization
```

---

# 58. Key Takeaways

- **Entity Framework is an ORM for .NET.**
- **EF Core is the modern EF technology for modern .NET applications.**
- An **entity class** represents database data.
- `DbContext` is the primary database interaction/session abstraction.
- `DbSet<TEntity>` represents a set/table of entities.
- LINQ allows querying through C#.
- EF Core translates LINQ into SQL.
- `SaveChanges()` persists tracked changes.
- Change tracking detects modifications to entities.
- `AsNoTracking()` is useful for read-only queries.
- `Include()` loads related entities.
- Migrations manage schema changes in Code First workflows.
- EF Core does not eliminate the need to understand SQL.
- Good EF Core development requires understanding both **C# and relational databases**.

---

# 59. One-Line Definition

> **Entity Framework Core is a modern .NET ORM that maps C# objects to relational database data and allows developers to query and modify that data using C# and LINQ.**
