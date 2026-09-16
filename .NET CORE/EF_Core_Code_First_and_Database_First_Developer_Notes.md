# Entity Framework Core: Code First and Database First
## Developer-Focused Notes for Real-World Projects

> **Purpose:** Practical, project-oriented documentation for developers working with Entity Framework Core (EF Core), especially in ASP.NET Core applications.

---

## 1. What Is Entity Framework Core?

**Entity Framework Core (EF Core)** is an Object-Relational Mapper (ORM) for .NET.

It allows application code to work with relational databases using C# objects instead of writing SQL for every operation.

### Without EF Core

You may write:

```csharp
using var command = new SqlCommand(
    "SELECT Id, Name FROM Employees WHERE Id = @id",
    connection);

command.Parameters.AddWithValue("@id", employeeId);
```

You manually deal with:

- SQL queries
- Connections
- Commands
- Parameters
- Data readers
- Mapping rows to C# objects

### With EF Core

You can write:

```csharp
var employee = await context.Employees
    .FirstOrDefaultAsync(e => e.Id == employeeId);
```

EF Core translates the LINQ expression into SQL, executes it, and materializes the result into a C# object.

---

# 2. The Core EF Core Components

Before understanding Code First and Database First, understand these components.

## 2.1 Entity

An **entity** is a C# class representing data that is usually mapped to a database table.

```csharp
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Department { get; set; }
    public decimal Salary { get; set; }
}
```

Potential database mapping:

```text
Employee class
       ↓
Employees table

Id          → Id
Name        → Name
Department  → Department
Salary      → Salary
```

---

## 2.2 DbContext

`DbContext` is the main EF Core class responsible for:

- Database connection management
- Entity mapping
- Query execution
- Change tracking
- Saving changes
- Transactions
- Model configuration

Example:

```csharp
public class AppDbContext : DbContext
{
    public AppDbContext(
        DbContextOptions<AppDbContext> options)
        : base(options)
    {
    }

    public DbSet<Employee> Employees { get; set; }
}
```

Think of it as the **bridge between your application and database**.

```text
Application
     ↓
 DbContext
     ↓
 EF Core
     ↓
Database Provider
     ↓
SQL Server
```

---

## 2.3 DbSet

A `DbSet<TEntity>` represents a collection of entities that EF Core can query and persist.

```csharp
public DbSet<Employee> Employees { get; set; }
```

This commonly maps to:

```text
Employees table
```

Example:

```csharp
var employees = await context.Employees.ToListAsync();
```

---

## 2.4 Database Provider

EF Core itself does not directly know how to communicate with every database.

You install a provider.

For SQL Server:

```bash
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

Other providers include providers for databases such as PostgreSQL, SQLite, and MySQL/MariaDB.

---

# 3. Two Main Development Approaches

EF Core commonly works in two directions.

## Code First

```text
C# Classes
    ↓
EF Core Model
    ↓
Migrations
    ↓
Database Schema
```

The application/domain model is the starting point.

---

## Database First

```text
Existing Database
       ↓
Reverse Engineering / Scaffold
       ↓
C# Entities + DbContext
       ↓
Application
```

The database schema is the starting point.

---

# 4. Code First Approach

## 4.1 Definition

In the **Code First approach**, developers define the application's entity model in C#.

EF Core uses those models and configuration to create or modify the database schema.

The normal workflow is:

```text
Requirements
     ↓
Entity Classes
     ↓
DbContext
     ↓
Model Configuration
     ↓
Migration
     ↓
Database Update
     ↓
Database
```

---

# 5. Code First Project Structure

A typical project might look like:

```text
MyApi/
│
├── Controllers/
│   └── EmployeesController.cs
│
├── Models/
│   └── Employee.cs
│
├── Data/
│   └── AppDbContext.cs
│
├── Migrations/
│   ├── 202609160001_InitialCreate.cs
│   └── AppDbContextModelSnapshot.cs
│
├── Services/
│   └── EmployeeService.cs
│
├── appsettings.json
└── Program.cs
```

Large projects may separate:

```text
Domain
Infrastructure
Application
API
```

but the EF Core concepts remain the same.

---

# 6. Creating an Entity

Example:

```csharp
public class Employee
{
    public int Id { get; set; }

    public string Name { get; set; }

    public string Department { get; set; }

    public decimal Salary { get; set; }

    public DateTime DateOfJoining { get; set; }
}
```

EF Core conventionally interprets:

```text
Id
```

as the primary key.

It can also recognize:

```text
EmployeeId
```

as a conventional primary key.

---

# 7. Creating DbContext

```csharp
public class AppDbContext : DbContext
{
    public AppDbContext(
        DbContextOptions<AppDbContext> options)
        : base(options)
    {
    }

    public DbSet<Employee> Employees { get; set; }
}
```

The constructor receives database configuration through dependency injection.

---

# 8. Registering DbContext

In `Program.cs`:

```csharp
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection")));
```

Connection string:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\MSSQLLocalDB;Database=EmployeeDb;Trusted_Connection=True;TrustServerCertificate=True"
  }
}
```

The flow is:

```text
appsettings.json
       ↓
Configuration
       ↓
Program.cs
       ↓
AddDbContext()
       ↓
DI Container
       ↓
AppDbContext
```

---

# 9. Migrations

Migrations are one of the most important parts of Code First.

A migration records changes that need to be applied to the database schema.

Suppose the original model is:

```csharp
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

Later you add:

```csharp
public string Email { get; set; }
```

You create a migration:

```bash
dotnet ef migrations add AddEmployeeEmail
```

Then:

```bash
dotnet ef database update
```

Conceptually:

```text
Old Model
   ↓
Model Change
   ↓
New Migration
   ↓
Database Update
```

---

# 10. EF Core Migration Commands

Install EF CLI if required:

```bash
dotnet tool install --global dotnet-ef
```

Add migration:

```bash
dotnet ef migrations add InitialCreate
```

Update database:

```bash
dotnet ef database update
```

List migrations:

```bash
dotnet ef migrations list
```

Remove the latest migration:

```bash
dotnet ef migrations remove
```

Generate SQL:

```bash
dotnet ef migrations script
```

Generate SQL from one migration to another:

```bash
dotnet ef migrations script PreviousMigration NewMigration
```

This is useful for controlled deployment environments.

---

# 11. What Is Actually Inside a Migration?

A migration commonly contains two important methods:

```csharp
protected override void Up(MigrationBuilder migrationBuilder)
{
    // Apply changes
}

protected override void Down(MigrationBuilder migrationBuilder)
{
    // Reverse changes
}
```

Example:

```csharp
protected override void Up(MigrationBuilder migrationBuilder)
{
    migrationBuilder.AddColumn<string>(
        name: "Email",
        table: "Employees",
        nullable: true);
}
```

`Down()` describes how to reverse that migration.

This gives migrations a history such as:

```text
InitialCreate
      ↓
AddEmployeeEmail
      ↓
AddDepartment
      ↓
AddEmployeeIndex
```

---

# 12. Model Snapshot

EF Core maintains a model snapshot.

Example:

```text
Migrations/
│
├── 202609160001_InitialCreate.cs
├── 202609160002_AddEmployeeEmail.cs
└── AppDbContextModelSnapshot.cs
```

The snapshot represents the current EF Core model.

It helps EF Core determine what changed when creating the next migration.

---

# 13. Data Annotations

You can configure entities using attributes.

```csharp
public class Employee
{
    [Key]
    public int EmployeeId { get; set; }

    [Required]
    [MaxLength(100)]
    public string Name { get; set; }

    [MaxLength(100)]
    public string Department { get; set; }
}
```

Common attributes:

```text
[Key]
[Required]
[MaxLength]
[MinLength]
[Column]
[Table]
[ForeignKey]
[DatabaseGenerated]
```

---

# 14. Fluent API

For larger applications, Fluent API is often preferred for complex mappings.

Example:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Employee>(entity =>
    {
        entity.HasKey(e => e.Id);

        entity.Property(e => e.Name)
              .IsRequired()
              .HasMaxLength(100);

        entity.Property(e => e.Salary)
              .HasColumnType("decimal(18,2)");
    });
}
```

Fluent API is useful for:

- Composite keys
- Relationships
- Indexes
- Constraints
- Table names
- Column names
- Precision
- Complex mappings

---

# 15. Relationships in Code First

Suppose one department has many employees.

```csharp
public class Department
{
    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Employee> Employees { get; set; }
}
```

Employee:

```csharp
public class Employee
{
    public int Id { get; set; }

    public string Name { get; set; }

    public int DepartmentId { get; set; }

    public Department Department { get; set; }
}
```

Relationship:

```text
Department
    1
    |
    |
    *
Employee
```

Configure explicitly if required:

```csharp
modelBuilder.Entity<Employee>()
    .HasOne(e => e.Department)
    .WithMany(d => d.Employees)
    .HasForeignKey(e => e.DepartmentId);
```

---

# 16. Code First: Typical Developer Workflow

When adding a new feature:

### Step 1 — Understand requirement

Example:

> Employee needs an email address.

### Step 2 — Change model

```csharp
public string Email { get; set; }
```

### Step 3 — Configure mapping if necessary

```csharp
entity.Property(e => e.Email)
      .HasMaxLength(200);
```

### Step 4 — Create migration

```bash
dotnet ef migrations add AddEmployeeEmail
```

### Step 5 — Review migration

Do not blindly apply generated migrations in production.

### Step 6 — Generate/review SQL

```bash
dotnet ef migrations script
```

### Step 7 — Apply migration

Development:

```bash
dotnet ef database update
```

Production:

Use your organization's deployment/migration process.

---

# 17. Code First: Advantages

### 17.1 Application and schema evolve together

The C# model is directly connected to schema changes.

### 17.2 Strongly typed development

You work with:

```csharp
Employee
Department
Order
Customer
```

instead of manually mapping database rows.

### 17.3 Migration history

Database changes can be version-controlled.

### 17.4 Excellent for new applications

You can design the model before creating the physical database.

### 17.5 Easy local development

A developer can create/update a local database using migrations.

---

# 18. Code First: Disadvantages

### 18.1 Existing complex databases can be difficult

If a database already has:

- Hundreds of tables
- Stored procedures
- Views
- Triggers
- Legacy naming
- Complex constraints

starting from Code First may be impractical.

### 18.2 Migration mistakes can affect production

A poorly designed migration can cause:

- Data loss
- Long table locks
- Failed deployments
- Downtime

### 18.3 Database-specific features may require extra configuration

Not every database feature maps naturally to an EF Core model.

---

# 19. Database First Approach

## 19.1 Definition

In the **Database First approach**, the database already exists.

EF Core reads the database schema and generates:

- Entity classes
- DbContext
- Relationships
- Keys
- Column mappings

This process is called **reverse engineering** or **scaffolding**.

---

# 20. Database First Workflow

```text
Existing Database
       ↓
Connection String
       ↓
EF Core Scaffold
       ↓
DbContext + Entity Classes
       ↓
Application Code
```

Example:

```text
SQL Server
   │
   ├── Employees
   ├── Departments
   ├── Projects
   └── EmployeeProjects
          ↓
       Scaffold
          ↓
Models + DbContext
```

---

# 21. Installing Database First Packages

For SQL Server:

```bash
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

For scaffolding:

```bash
dotnet add package Microsoft.EntityFrameworkCore.Design
```

The exact package/tool versions should normally match the EF Core version used by the project.

---

# 22. Scaffold DbContext

Basic command:

```bash
dotnet ef dbcontext scaffold "YOUR_CONNECTION_STRING" Microsoft.EntityFrameworkCore.SqlServer
```

Example:

```bash
dotnet ef dbcontext scaffold \
"Server=(localdb)\MSSQLLocalDB;Database=EmployeeDb;Trusted_Connection=True;TrustServerCertificate=True" \
Microsoft.EntityFrameworkCore.SqlServer
```

This can generate:

```text
Data/
   AppDbContext.cs

Models/
   Employee.cs
   Department.cs
```

---

# 23. Useful Scaffold Options

Specify output directory:

```bash
dotnet ef dbcontext scaffold "CONNECTION_STRING" Microsoft.EntityFrameworkCore.SqlServer \
--output-dir Models
```

Specify DbContext directory:

```bash
--context-dir Data
```

Specify DbContext name:

```bash
--context AppDbContext
```

Generate only selected tables:

```bash
--table Employees
```

Multiple tables:

```bash
--table Employees \
--table Departments
```

Overwrite existing generated files:

```bash
--force
```

Use data annotations:

```bash
--data-annotations
```

Avoid generating connection string in source:

```bash
--no-onconfiguring
```

A commonly useful command is:

```bash
dotnet ef dbcontext scaffold "CONNECTION_STRING" \
Microsoft.EntityFrameworkCore.SqlServer \
--context AppDbContext \
--context-dir Data \
--output-dir Models \
--no-onconfiguring
```

---

# 24. What Does Scaffolding Generate?

Suppose the database contains:

```sql
CREATE TABLE Employees
(
    Id INT PRIMARY KEY,
    Name NVARCHAR(100) NOT NULL,
    Salary DECIMAL(18,2)
);
```

EF Core may generate:

```csharp
public partial class Employee
{
    public int Id { get; set; }

    public string Name { get; set; }

    public decimal? Salary { get; set; }
}
```

And:

```csharp
public partial class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options)
        : base(options)
    {
    }

    public virtual DbSet<Employee> Employees { get; set; }
}
```

---

# 25. Database First and Existing Database Changes

This is one of the most important practical points.

Suppose the database changes:

```text
Employees
    ↓
New column: Email
```

Your generated model may now be outdated.

You can scaffold the database again.

Conceptually:

```text
Database changes
       ↓
Re-scaffold
       ↓
Updated Models
```

But this creates an important development problem:

> **Do not put custom business logic directly into generated files if re-scaffolding can overwrite it.**

---

# 26. Partial Classes

Generated EF Core classes can often be declared as:

```csharp
public partial class Employee
{
    ...
}
```

You can extend them in another file:

```csharp
public partial class Employee
{
    public string DisplayName =>
        $"{Name} - {Department}";
}
```

This helps keep generated code separate from custom code.

Example:

```text
Models/
│
├── Employee.cs              ← Generated
└── Employee.Custom.cs       ← Developer code
```

When re-scaffolding:

```text
Employee.cs
```

may be regenerated, while:

```text
Employee.Custom.cs
```

remains untouched.

---

# 27. Database First: Existing Database Is Usually the Source of Truth

In a traditional Database First project:

```text
Database Schema
      ↓
Source of Truth
      ↓
EF Core Models
```

If the DBA changes:

```text
Column
Index
Foreign Key
Table
Data Type
```

the application's EF model may need to be regenerated or manually updated.

---

# 28. Code First vs Database First: Source of Truth

This is the most important conceptual difference.

## Code First

```text
C# Model
   ↓
Migrations
   ↓
Database
```

Source of truth is primarily:

```text
Application model + migration history
```

## Database First

```text
Database
   ↓
Scaffold
   ↓
C# Model
```

Source of truth is primarily:

```text
Database schema
```

---

# 29. Important: Database First Does Not Mean EF Core Cannot Modify Data

Database First only describes **how the schema/model is initially obtained**.

You can still perform:

```csharp
context.Employees.Add(employee);

await context.SaveChangesAsync();
```

or:

```csharp
var employees = await context.Employees.ToListAsync();
```

The CRUD operations are still handled by EF Core.

The difference is primarily the **schema/model development workflow**.

---

# 30. CRUD With EF Core

Regardless of Code First or Database First, once the model exists, EF Core CRUD is similar.

## Create

```csharp
var employee = new Employee
{
    Name = "Raj",
    Department = "IT",
    Salary = 50000
};

context.Employees.Add(employee);

await context.SaveChangesAsync();
```

---

## Read

```csharp
var employee = await context.Employees
    .FirstOrDefaultAsync(e => e.Id == id);
```

---

## Update

```csharp
var employee = await context.Employees
    .FindAsync(id);

employee.Name = "Rahul";

await context.SaveChangesAsync();
```

---

## Delete

```csharp
var employee = await context.Employees
    .FindAsync(id);

context.Employees.Remove(employee);

await context.SaveChangesAsync();
```

---

# 31. Tracking

EF Core normally tracks entities retrieved through a `DbContext`.

Example:

```csharp
var employee = await context.Employees
    .FirstAsync(e => e.Id == 1);

employee.Salary = 60000;

await context.SaveChangesAsync();
```

You did not explicitly execute:

```sql
UPDATE Employees
SET Salary = 60000
WHERE Id = 1;
```

EF Core detects the changed property and generates the appropriate SQL.

Conceptually:

```text
Database
   ↓
Employee object
   ↓
Change Tracker
   ↓
Property changed
   ↓
SaveChanges()
   ↓
UPDATE SQL
```

---

# 32. AsNoTracking

For read-only queries:

```csharp
var employees = await context.Employees
    .AsNoTracking()
    .ToListAsync();
```

This tells EF Core not to track the returned entities.

Useful when:

- Data is only being displayed
- You don't intend to update the entities
- You want to reduce tracking overhead for large read-heavy queries

---

# 33. LINQ and SQL Translation

Example:

```csharp
var employees = await context.Employees
    .Where(e => e.Salary > 50000)
    .OrderBy(e => e.Name)
    .ToListAsync();
```

EF Core translates this into SQL similar to:

```sql
SELECT *
FROM Employees
WHERE Salary > 50000
ORDER BY Name;
```

The exact generated SQL depends on the provider and query.

---

# 34. Database First With Stored Procedures

Existing enterprise databases often contain:

- Stored procedures
- Views
- Functions
- Triggers

EF Core can work with such databases, but the mapping and invocation strategy depends on the database object and project architecture.

For example, raw SQL can be executed through EF Core:

```csharp
var employees = await context.Employees
    .FromSqlRaw("EXEC GetEmployees")
    .ToListAsync();
```

For parameterized SQL, prefer parameterized APIs rather than string concatenation.

---

# 35. Views

Suppose the database contains:

```sql
CREATE VIEW EmployeeSummary AS
SELECT
    Id,
    Name,
    Department
FROM Employees;
```

A Database First project can map a model to that view.

Conceptually:

```text
EmployeeSummary
       ↓
EF Core entity/keyless entity
       ↓
Application
```

A view is generally treated differently from a normal table because it is commonly read-only from the application's perspective.

---

# 36. Keyless Entities

Some database objects do not have a primary key.

EF Core supports keyless entity types.

Example:

```csharp
[Keyless]
public class EmployeeSummary
{
    public string Name { get; set; }
    public string Department { get; set; }
}
```

These are useful for:

- Views
- SQL query results
- Read-only projections

---

# 37. Database First and Stored Procedures: Important Enterprise Reality

A legacy database may have:

```text
Tables
Views
Stored Procedures
Functions
Triggers
Indexes
Constraints
```

The application may use a mixture of:

```text
EF Core LINQ
+
Stored Procedures
+
Raw SQL
+
Views
```

Therefore, do not assume:

> "Database First means every database operation must be done through generated EF classes."

The actual project architecture determines how database objects are consumed.

---

# 38. Handling Generated Code in Database First

Avoid making extensive manual modifications to generated files.

Bad approach:

```text
Employee.cs
    ↓
Generated
    ↓
Developer adds 200 lines of custom business logic
    ↓
Re-scaffold
    ↓
Custom changes potentially lost
```

Better:

```text
Generated model
      +
Partial class
      +
Service layer
      +
DTOs
```

Keep business logic outside generated persistence models where practical.

---

# 39. DbContext Lifetime

In ASP.NET Core, `DbContext` is commonly registered as scoped:

```csharp
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(connectionString));
```

This generally gives one context instance per request scope.

Do not treat `DbContext` as a global singleton.

---

# 40. Dependency Injection

Controller:

```csharp
public class EmployeesController : ControllerBase
{
    private readonly AppDbContext _context;

    public EmployeesController(AppDbContext context)
    {
        _context = context;
    }
}
```

Flow:

```text
HTTP Request
     ↓
Controller
     ↓
DbContext injected by DI
     ↓
EF Core
     ↓
SQL Server
```

---

# 41. DTOs Are Still Important

Do not assume:

```text
Entity = API Response
```

They are different concerns.

Entity:

```csharp
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Salary { get; set; }
}
```

DTO:

```csharp
public class EmployeeResponseDto
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

DTOs can prevent:

- Exposing internal database fields
- Over-posting
- Tight coupling between API contracts and database schema
- Accidental exposure of sensitive/internal properties

This applies to both Code First and Database First.

---

# 42. Entity vs DTO

```text
Database
   ↓
EF Entity
   ↓
Service
   ↓
DTO
   ↓
Controller
   ↓
API Client
```

A clean API often follows:

```text
Database Model ≠ API Contract
```

---

# 43. Code First in a Real Project

Suppose your team receives this requirement:

> Add employee email and make it unique.

You may implement:

### Entity

```csharp
public class Employee
{
    public int Id { get; set; }

    public string Name { get; set; }

    public string Email { get; set; }
}
```

### Fluent configuration

```csharp
modelBuilder.Entity<Employee>()
    .HasIndex(e => e.Email)
    .IsUnique();
```

### Migration

```bash
dotnet ef migrations add AddUniqueEmployeeEmail
```

### Review migration

Check:

- Does it create the intended index?
- Is existing data compatible?
- Can existing duplicate emails cause failure?
- Could the operation lock a large table?

### Deploy

Apply through the project's deployment process.

---

# 44. Code First Production Considerations

Never think:

```text
Change Model
    ↓
dotnet ef database update
```

is automatically safe for production.

Before applying schema changes, consider:

### Data compatibility

If adding:

```sql
NOT NULL
```

to a large existing table, existing rows need valid values.

### Large tables

Adding indexes or altering columns can take significant time.

### Breaking changes

Changing:

```text
nvarchar → int
```

may require a data migration.

### Deployment ordering

For application/database compatibility, a safer strategy can be:

```text
1. Make database backward-compatible
2. Deploy application
3. Migrate data if required
4. Remove old schema later
```

The exact deployment strategy depends on the application.

---

# 45. Database First Production Considerations

With Database First:

```text
DBA / Database Team
       ↓
Schema Change
       ↓
Scaffold / Model Update
       ↓
Application Changes
       ↓
Testing
       ↓
Deployment
```

Developers should understand database changes before changing application code.

---

# 46. Re-Scaffolding Existing Database

Suppose the database currently contains:

```text
Employees
Departments
Projects
```

You scaffold.

Later:

```text
Employees
    + Email

Departments
    + Location
```

You can scaffold again.

However, understand what `--force` does:

```bash
dotnet ef dbcontext scaffold "CONNECTION_STRING" \
Microsoft.EntityFrameworkCore.SqlServer \
--force
```

It can overwrite generated files.

Therefore:

> Keep generated code separate from custom code.

---

# 47. Scaffold Only Required Tables

You don't necessarily need to scaffold every table.

Example:

```bash
dotnet ef dbcontext scaffold "CONNECTION_STRING" \
Microsoft.EntityFrameworkCore.SqlServer \
--table Employees \
--table Departments \
--output-dir Models
```

This can be useful when working with a large database.

---

# 48. Scaffold Naming and Existing Conventions

Enterprise databases may use names such as:

```text
EMP_MST
EMP_ID
EMP_NM
CRT_DT
MOD_DT
IS_DEL
```

Scaffolded C# models may therefore need configuration or naming adjustments.

Do not casually rename properties without understanding the mapping.

Example:

```csharp
entity.Property(e => e.Name)
      .HasColumnName("EMP_NM");
```

This allows:

```text
C#:
Name

Database:
EMP_NM
```

---

# 49. Common Database First Problems

## Problem 1: Database changed but model didn't

```text
Database
   ↓
Column added
   ↓
Old EF model
   ↓
Application doesn't know about column
```

Solution:

- Re-scaffold where appropriate
- Or update the mapping manually

---

## Problem 2: Re-scaffolding overwrites custom code

Solution:

- Partial classes
- Separate service/business logic
- Keep generated files generated

---

## Problem 3: Huge database

Scaffolding hundreds of tables may produce a large model.

Solution:

- Scaffold required tables
- Understand bounded contexts/modules
- Avoid unnecessary coupling

---

# 50. Code First Common Problems

## Problem 1: Wrong migration

You created:

```bash
dotnet ef migrations add Test
```

but the migration is incorrect.

Review before applying.

If it has not been applied:

```bash
dotnet ef migrations remove
```

Then correct the model/configuration and create a new migration.

---

## Problem 2: Migration already applied

Do not casually delete migrations from a shared/production history.

Database migration history matters.

---

## Problem 3: Data loss warning

EF Core may detect operations that could cause data loss.

For example:

```text
Rename column incorrectly
```

may be interpreted as:

```text
Drop old column
+
Add new column
```

instead of an actual rename.

Always inspect generated migrations.

---

# 51. Migration vs Scaffold

These two concepts are frequently confused.

## Migration

Used primarily in Code First to move the database schema between model versions.

```text
C# Model
   ↓
Migration
   ↓
Database
```

## Scaffold

Used in Database First to reverse-engineer a database into C#.

```text
Database
   ↓
Scaffold
   ↓
C# Model
```

Remember:

```text
Migration = forward schema evolution

Scaffold = reverse engineering
```

---

# 52. Can Code First Work With an Existing Database?

Yes.

"Code First" does not strictly mean:

> The database must never exist before the application.

You can create a model for an existing database and manage future changes through migrations, but adopting this approach for an established production schema requires careful planning.

You must ensure the EF model and migration history accurately represent the database's current state.

For a large legacy database, this should be treated as a deliberate migration project rather than simply running `database update`.

---

# 53. Can Database First Use Migrations?

Technically, EF Core supports migrations based on the model regardless of how the initial model was obtained.

However, in a traditional Database First workflow, the database is usually the source of truth and schema changes are managed outside the EF migration workflow.

Do not automatically assume:

```text
Database First = No migrations ever
```

The real distinction is the **schema ownership/workflow**, not a hard technical restriction.

---

# 54. Hybrid Approach

Real projects do not always fit perfectly into one category.

You may see:

```text
Existing Database
       ↓
Initial Scaffold
       ↓
EF Core Models
       ↓
Custom mappings
       ↓
EF Core application
```

while database changes are handled by:

```text
DB scripts
+
DBA deployment
+
EF Core model updates
```

Another team might use:

```text
Code First
+
Migrations
+
Some existing stored procedures
+
Views
+
Raw SQL
```

Therefore, always understand the project's actual database governance.

---

# 55. Recommended Architecture for ASP.NET Core

A common structure is:

```text
                    ┌───────────────┐
                    │   Controller  │
                    └───────┬───────┘
                            ↓
                    ┌───────────────┐
                    │    Service    │
                    └───────┬───────┘
                            ↓
                    ┌───────────────┐
                    │  Repository   │
                    │  (if used)    │
                    └───────┬───────┘
                            ↓
                    ┌───────────────┐
                    │   DbContext   │
                    └───────┬───────┘
                            ↓
                    ┌───────────────┐
                    │   EF Core     │
                    └───────┬───────┘
                            ↓
                    ┌───────────────┐
                    │ SQL Server     │
                    └───────────────┘
```

A repository layer is not mandatory; EF Core's `DbContext` already provides data-access abstractions. Use an additional repository layer only when it provides architectural value.

---

# 56. Code First vs Database First — Detailed Comparison

| Area | Code First | Database First |
|---|---|---|
| Initial source | C# model | Existing database |
| Main direction | Code → DB | DB → Code |
| Schema ownership | Application/team | Database/DBA/team |
| Migrations | Common | Project-dependent |
| Scaffold | Usually not initial workflow | Core workflow |
| New application | Common fit | Less common |
| Legacy database | Can be difficult | Common fit |
| Generated entities | Usually manually created | Usually generated |
| Schema changes | Model → migration → DB | DB → scaffold/model update |
| Database control | Developer/application team | Database team often controls |
| Complex legacy DB | More difficult | Often practical |
| Versioning | Migrations + code | DB scripts + model code |
| Custom generated-code handling | Not usually an issue | Important |
| Stored procedures | Can be used | Common in legacy systems |
| Views | Can be mapped | Common |
| DTO usage | Recommended | Recommended |

---

# 57. How to Decide Which Approach a Project Uses

When joining an existing project, inspect:

### 1. Look for `Migrations`

```text
Migrations/
```

If present, the project may use Code First or migrations as part of its schema workflow.

### 2. Look at entity classes

Are they clearly generated?

Look for:

```csharp
public partial class Employee
```

and generated comments.

### 3. Look at DbContext

Does it contain:

```csharp
protected override void OnModelCreating(...)
```

with mappings for an existing database?

### 4. Check database ownership

Ask:

> Who owns database schema changes?

Application team?

DBA team?

Both?

### 5. Check deployment scripts

Look for:

```text
.sql files
Database deployment projects
Migration pipelines
Stored procedure scripts
```

### 6. Look at project documentation/CI/CD

The repository may define the actual database deployment process.

---

# 58. Questions a Developer Should Ask on an Existing Project

Before modifying EF Core code, understand:

```text
1. Is this Code First or Database First?
2. What is the source of truth for the schema?
3. Are migrations used?
4. Who applies database changes?
5. Are migrations applied automatically in CI/CD?
6. Are SQL scripts used for production?
7. Are entities generated?
8. Can generated files be edited?
9. Are stored procedures used?
10. Are views mapped to EF entities?
11. Is DbContext scoped?
12. Is there a repository/service layer?
13. Which EF Core version is being used?
14. Which database provider is used?
15. Are there multiple DbContexts?
```

These questions are often more useful than simply knowing the definition of Code First.

---

# 59. Practical Example — Code First

Requirement:

> Create an Employee management API.

### Step 1

Create:

```csharp
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Department { get; set; }
}
```

### Step 2

Create:

```csharp
public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options)
        : base(options)
    {
    }

    public DbSet<Employee> Employees { get; set; }
}
```

### Step 3

Configure SQL Server.

### Step 4

Create migration:

```bash
dotnet ef migrations add InitialCreate
```

### Step 5

Update database:

```bash
dotnet ef database update
```

### Result

```text
Employee.cs
     ↓
DbContext
     ↓
Migration
     ↓
SQL Server
     ↓
Employees table
```

---

# 60. Practical Example — Database First

Suppose your company already has:

```text
EmployeeDB
│
├── Employees
├── Departments
├── Attendance
├── Payroll
├── EmployeeDocuments
└── Stored Procedures
```

You don't create all these tables again.

Instead:

```bash
dotnet ef dbcontext scaffold "CONNECTION_STRING" \
Microsoft.EntityFrameworkCore.SqlServer \
--output-dir Models \
--context-dir Data \
--context EmployeeDbContext \
--no-onconfiguring
```

EF Core generates:

```text
Models/
    Employee.cs
    Department.cs
    Attendance.cs
    Payroll.cs
    EmployeeDocument.cs

Data/
    EmployeeDbContext.cs
```

You then build:

```text
Controller
    ↓
Service
    ↓
DbContext
    ↓
Existing SQL Server Database
```

---

# 61. Important Difference in Development Thinking

### Code First developer thinks:

> "I need to change the model. What database migration should this produce?"

```text
Requirement
   ↓
Model change
   ↓
Migration
   ↓
Database
```

### Database First developer thinks:

> "The database changed. How does my EF model need to reflect that change?"

```text
Database change
   ↓
Scaffold/model update
   ↓
Application changes
```

This difference is extremely important when working on a team.

---

# 62. Common Interview Questions

## Q1. What is Code First?

> Code First is an EF Core development approach where C# entity classes and configuration define the model, and migrations are commonly used to create and evolve the database schema.

## Q2. What is Database First?

> Database First starts with an existing database. EF Core reverse-engineers the schema using scaffolding and generates entity classes and a DbContext.

## Q3. What is scaffolding?

> Scaffolding is the reverse-engineering process of generating EF Core entity classes and DbContext from an existing database.

## Q4. What is a migration?

> A migration is a versioned representation of changes to the EF Core model that can be applied to the database schema.

## Q5. Difference between migration and scaffold?

> Migration moves the database schema according to model changes, while scaffolding generates the model from an existing database schema.

## Q6. Can Database First use EF Core?

> Yes. Database First is one of the primary EF Core workflows and uses reverse engineering/scaffolding.

## Q7. Can Code First work with an existing database?

> Yes, but adopting migrations against an existing database requires careful alignment of the EF model, migration history, and actual schema.

## Q8. Why use DTOs?

> DTOs separate the API contract from persistence entities and help control which data enters or leaves the API.

---

# 63. Developer Mental Model

Remember these three layers:

```text
             APPLICATION
                  │
                  ↓
            EF CORE MODEL
                  │
                  ↓
              DATABASE
```

### Code First

```text
Application Model
       ↓
EF Core
       ↓
Migrations
       ↓
Database
```

### Database First

```text
Database
   ↓
Scaffold
   ↓
EF Core Model
   ↓
Application
```

---

# 64. Final Summary

## Code First

Use when the application/team is driving the schema.

```text
C# Entity
   ↓
DbContext
   ↓
Configuration
   ↓
Migration
   ↓
Database
```

Main tools:

```bash
dotnet ef migrations add
dotnet ef database update
dotnet ef migrations script
```

---

## Database First

Use when the database already exists and the database schema drives the application model.

```text
Existing Database
       ↓
Scaffold
       ↓
DbContext
       ↓
Entities
       ↓
Application
```

Main tool:

```bash
dotnet ef dbcontext scaffold
```

---

# 65. The One-Line Difference

> **Code First:** "I define the model in C#, and EF Core helps evolve the database."

> **Database First:** "The database already defines the schema, and EF Core generates/reverse-engineers the C# model."

---

# 66. What You Should Be Comfortable With as a Project Developer

For practical ASP.NET Core development, don't stop at the definitions. You should be comfortable with:

```text
EF Core
│
├── Entity classes
├── DbContext
├── DbSet
├── Dependency Injection
├── Connection strings
├── SQL Server provider
│
├── CODE FIRST
│   ├── Model configuration
│   ├── Fluent API
│   ├── Data annotations
│   ├── Migrations
│   ├── Migration review
│   └── Database deployment
│
├── DATABASE FIRST
│   ├── Scaffold
│   ├── Reverse engineering
│   ├── Generated models
│   ├── Partial classes
│   ├── Re-scaffolding
│   └── Existing DB changes
│
├── DATA ACCESS
│   ├── LINQ
│   ├── CRUD
│   ├── Tracking
│   ├── AsNoTracking
│   ├── Transactions
│   ├── Raw SQL
│   ├── Stored procedures
│   └── Views
│
└── API ARCHITECTURE
    ├── Entities
    ├── DTOs
    ├── Services
    ├── Controllers
    └── Database
```

If you understand this entire flow, you can work with EF Core in both **new ASP.NET Core applications** and **existing enterprise/legacy database-driven projects**.
