# Stored Procedures in SQL Server

## 1. What is a Stored Procedure?

A **Stored Procedure** is a set of SQL statements that is **stored inside the database** and can be executed by calling its name.

Think of it like a **function in programming**, but the function lives inside the database.

### C#

```csharp
void GetEmployees()
{
    // code
}
```

### SQL Server

```sql
CREATE PROCEDURE GetEmployees
AS
BEGIN
    SELECT *
    FROM Employees;
END
```

Execute it:

```sql
EXEC GetEmployees;
```

### Basic idea

```text
C# Function
    ↓
Stored Procedure
    ↓
SQL statements
    ↓
Database
```

---

## 2. Why are they called "Stored" Procedures?

Because the procedure is **stored in the database**.

Example:

```text
MyCompanyDB
│
├── Tables
│   ├── Employees
│   ├── Departments
│   └── Projects
│
├── Views
│
└── Stored Procedures
    ├── GetEmployees
    ├── GetEmployeeById
    ├── InsertEmployee
    ├── UpdateEmployee
    └── DeleteEmployee
```

The stored procedure lives inside SQL Server, not inside the `.aspx.cs` file.

---

## 3. Why do companies use Stored Procedures?

Without a stored procedure, C# might contain SQL directly:

```csharp
string query = "SELECT * FROM Employees";
```

With stored procedures, SQL logic can be kept inside the database.

```text
ASP.NET Application
        │
        │ calls
        ▼
Stored Procedure
        │
        │ executes SQL
        ▼
Database Tables
```

This creates separation between application logic and database logic.

---

## 4. Creating Your First Stored Procedure

Suppose we have:

```text
Employees

ID     Name       Department
--------------------------------
1      Raj        IT
2      Vedant     HR
3      Amit       Finance
```

Create a procedure:

```sql
CREATE PROCEDURE GetEmployees
AS
BEGIN

    SELECT *
    FROM Employees;

END
```

### Breaking it down

`CREATE PROCEDURE` creates a new procedure.

```sql
CREATE PROCEDURE GetEmployees
```

`AS` begins the procedure definition.

```sql
AS
```

`BEGIN ... END` contains the SQL statements executed by the procedure.

```sql
BEGIN
    SELECT *
    FROM Employees;
END
```

---

## 5. Executing a Stored Procedure

Once created:

```sql
EXEC GetEmployees;
```

or:

```sql
EXECUTE GetEmployees;
```

Both mean:

> Run the stored procedure.

Result:

```text
ID    Name       Department
----------------------------
1     Raj        IT
2     Vedant     HR
3     Amit       Finance
```

---

## 6. Stored Procedure with a Parameter

You can pass values into a stored procedure.

```sql
CREATE PROCEDURE GetEmployeeById
    @EmployeeId INT
AS
BEGIN

    SELECT *
    FROM Employees
    WHERE ID = @EmployeeId;

END
```

Here:

```text
@EmployeeId
```

is a parameter.

It's similar to a function parameter in C#.

Execute:

```sql
EXEC GetEmployeeById 2;
```

This means:

> Run `GetEmployeeById` where `EmployeeId = 2`.

---

## 7. Multiple Parameters

Example:

```sql
CREATE PROCEDURE SearchEmployees
    @Department VARCHAR(50),
    @MinId INT
AS
BEGIN

    SELECT *
    FROM Employees
    WHERE Department = @Department
      AND ID >= @MinId;

END
```

Execute:

```sql
EXEC SearchEmployees 'IT', 5;
```

---

## 8. Stored Procedure vs Normal SQL Query

Normal SQL:

```sql
SELECT *
FROM Employees
WHERE Department = 'IT';
```

Stored procedure:

```sql
CREATE PROCEDURE GetITEmployees
AS
BEGIN

    SELECT *
    FROM Employees
    WHERE Department = 'IT';

END
```

Execute:

```sql
EXEC GetITEmployees;
```

The stored procedure makes the database logic named, reusable, and centralized.

---

## 9. Stored Procedures and ASP.NET Web Forms

Typical flow:

```text
Employees.aspx
       │
       ▼
Employees.aspx.cs
       │
       ▼
C# / ADO.NET
       │
       ▼
SQL Server
       │
       ▼
Stored Procedure
       │
       ▼
Employees Table
```

For retrieving data:

```text
GridView
   ↑
   │ DataTable
   │
C# code
   ↑
   │
Stored Procedure
   ↑
   │
SQL Server
```

---

## 10. Calling a Stored Procedure from ASP.NET

Suppose:

```sql
CREATE PROCEDURE GetEmployees
AS
BEGIN
    SELECT ID, Name, Department
    FROM Employees;
END
```

C#:

```csharp
using (SqlConnection con = new SqlConnection(connectionString))
{
    using (SqlCommand cmd = new SqlCommand("GetEmployees", con))
    {
        cmd.CommandType = CommandType.StoredProcedure;

        con.Open();

        SqlDataAdapter da = new SqlDataAdapter(cmd);

        DataTable dt = new DataTable();

        da.Fill(dt);

        GridView1.DataSource = dt;
        GridView1.DataBind();
    }
}
```

### Flow

```text
Page_Load
    ↓
C# method
    ↓
SqlCommand
    ↓
Stored Procedure
    ↓
SQL Server
    ↓
Employees table
    ↓
DataTable
    ↓
GridView
```

---

## 11. What is SqlCommand?

```csharp
SqlCommand cmd =
    new SqlCommand("GetEmployees", con);
```

This creates a command that says:

> Execute `GetEmployees` using this database connection.

Then:

```csharp
cmd.CommandType = CommandType.StoredProcedure;
```

tells ADO.NET that the command is a stored procedure.

---

## 12. Stored Procedure with Parameters from C#

Stored procedure:

```sql
CREATE PROCEDURE GetEmployeeById
    @EmployeeId INT
AS
BEGIN

    SELECT *
    FROM Employees
    WHERE ID = @EmployeeId;

END
```

C#:

```csharp
SqlCommand cmd =
    new SqlCommand("GetEmployeeById", con);

cmd.CommandType = CommandType.StoredProcedure;

cmd.Parameters.AddWithValue("@EmployeeId", 2);
```

Then:

```csharp
cmd.ExecuteReader();
```

SQL Server receives:

```text
Procedure:
GetEmployeeById

Parameter:
@EmployeeId = 2
```

---

## 13. Why Parameters Are Important

Avoid constructing SQL like this:

```csharp
string query =
    "SELECT * FROM Employees WHERE Name = '" + name + "'";
```

This can lead to **SQL Injection** vulnerabilities.

Instead, use parameters:

```csharp
cmd.Parameters.AddWithValue("@Name", name);
```

Or preferably use an explicitly typed parameter:

```csharp
cmd.Parameters.Add("@Name", SqlDbType.VarChar, 100).Value = name;
```

Important:

> Using a stored procedure does not automatically guarantee protection against SQL injection. Unsafe dynamic SQL inside a stored procedure can still be vulnerable.

---

## 14. Stored Procedure for INSERT

Stored procedures can perform CRUD operations.

```sql
CREATE PROCEDURE InsertEmployee
    @Name VARCHAR(100),
    @Department VARCHAR(50)
AS
BEGIN

    INSERT INTO Employees
        (Name, Department)
    VALUES
        (@Name, @Department);

END
```

Execute:

```sql
EXEC InsertEmployee
    'Raj',
    'IT';
```

---

## 15. Stored Procedure for UPDATE

```sql
CREATE PROCEDURE UpdateEmployee
    @EmployeeId INT,
    @Name VARCHAR(100),
    @Department VARCHAR(50)
AS
BEGIN

    UPDATE Employees
    SET
        Name = @Name,
        Department = @Department
    WHERE ID = @EmployeeId;

END
```

Execute:

```sql
EXEC UpdateEmployee
    1,
    'Raj Pohekar',
    'Software';
```

---

## 16. Stored Procedure for DELETE

```sql
CREATE PROCEDURE DeleteEmployee
    @EmployeeId INT
AS
BEGIN

    DELETE FROM Employees
    WHERE ID = @EmployeeId;

END
```

Execute:

```sql
EXEC DeleteEmployee 3;
```

---

## 17. CRUD Stored Procedures

A typical CRUD application might have:

```text
Stored Procedures
│
├── GetEmployees
├── GetEmployeeById
├── InsertEmployee
├── UpdateEmployee
└── DeleteEmployee
```

Web Forms:

```text
Employee.aspx
Employee.aspx.cs
        │
        ├── LoadEmployees()
        ├── SaveEmployee()
        ├── UpdateEmployee()
        └── DeleteEmployee()
                 │
                 ▼
           SQL Server
                 │
                 ├── GetEmployees
                 ├── InsertEmployee
                 ├── UpdateEmployee
                 └── DeleteEmployee
```

---

## 18. Stored Procedure Can Return Data

A `SELECT` inside a procedure can return a result set.

```sql
CREATE PROCEDURE GetEmployees
AS
BEGIN
    SELECT ID, Name, Department
    FROM Employees;
END
```

Execute:

```sql
EXEC GetEmployees;
```

Result:

```text
ID    Name       Department
----------------------------
1     Raj        IT
2     Vedant     HR
3     Amit       Finance
```

C# can capture the result using:

```text
SqlDataReader
```

or:

```text
DataTable
```

Then bind it to:

```text
GridView
```

---

## 19. Ways a Stored Procedure Can Return Information

### 1. Result Set

```sql
SELECT ...
```

Returns rows and columns.

### 2. Output Parameter

```sql
@EmployeeCount INT OUTPUT
```

Returns a specific value through an output parameter.

### 3. RETURN Value

```sql
RETURN 1;
```

Usually used for a status/integer return code.

These are different concepts.

---

## 20. Why Companies Use Stored Procedures

### Centralized database logic

Instead of SQL being scattered throughout C#:

```text
C# file 1 → SQL
C# file 2 → SQL
C# file 3 → SQL
```

you can have:

```text
SQL Server
   ↓
Stored Procedures
   ├── GetEmployees
   ├── InsertEmployee
   ├── UpdateEmployee
   └── DeleteEmployee
```

### Reusability

Multiple application components can call the same procedure.

### Security

Permissions can sometimes be managed so application users execute procedures without having broad direct table permissions.

### Maintainability

Database logic can be changed in one place.

### Performance

Stored procedures can benefit from SQL Server's plan caching and other database optimizations.

However:

> **Stored procedures are not automatically faster than ad-hoc SQL.**

Performance depends on query design, indexes, execution plans, parameterization, amount of data, joins, database design, and SQL Server configuration.

---

## 21. Important Misconception

Do not think:

> "Stored procedure = faster SQL."

The core concept is:

```text
Stored Procedure
=
Named, reusable SQL program stored in the database
```

---

## 22. Stored Procedure vs C# Method

| C# | SQL Server |
|---|---|
| Method | Stored Procedure |
| Method parameter | Procedure parameter |
| Method body | SQL statements |
| Call method | `EXEC ProcedureName` |
| Return value | Result set / output parameter / return code |
| C# application | Database |

Example:

### C#

```csharp
GetEmployeeById(10);
```

### SQL

```sql
EXEC GetEmployeeById 10;
```

Both follow the basic idea:

```text
Give me an input
       ↓
Execute logic
       ↓
Give me output
```

---

## 23. Complete ASP.NET + Stored Procedure Picture

```text
                    ASP.NET WEB FORMS
                           │
                           ▼
                    Employee.aspx
                           │
                           ▼
                   Employee.aspx.cs
                           │
                           ▼
                    C# / ADO.NET
                           │
                    SqlConnection
                           │
                           ▼
                     SqlCommand
                           │
                           ▼
                  Stored Procedure
                           │
                           ▼
                      SQL Server
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
          SELECT         INSERT        UPDATE
             │             │             │
             └─────────────┼─────────────┘
                           ▼
                     Database Tables
                           │
                           ▼
                       DataTable
                           │
                           ▼
                       GridView
                           │
                           ▼
                        Browser
```

---

## 24. Learning Order

For ASP.NET Web Forms work, learn Stored Procedures in this order.

### Level 1 — Basics

- What is a stored procedure?
- `CREATE PROCEDURE`
- `ALTER PROCEDURE`
- `EXEC`
- `DROP PROCEDURE`
- Parameters
- `SELECT` inside a procedure

### Level 2 — CRUD

- INSERT procedure
- SELECT procedure
- UPDATE procedure
- DELETE procedure

### Level 3 — ADO.NET

```text
SqlConnection
      ↓
SqlCommand
      ↓
CommandType.StoredProcedure
      ↓
Parameters
      ↓
ExecuteReader / ExecuteNonQuery / ExecuteScalar
```

### Level 4 — Advanced

- Output parameters
- Return values
- Transactions
- `TRY...CATCH`
- Temporary tables
- Table variables
- Dynamic SQL
- `SCOPE_IDENTITY()`
- Error handling
- Query optimization
- Indexes
- Execution plans

---

# Key Takeaways

### Stored Procedure

> A named set of SQL statements stored inside SQL Server.

### `CREATE PROCEDURE`

> Creates a stored procedure.

### `EXEC`

> Executes a stored procedure.

### Parameters

> Allow values to be passed into the procedure.

### `DataSource`

> The data provided to a Web Forms control.

### `DataBind()`

> Tells the control to bind/process the supplied data.

### ASP.NET connection

```text
Web Forms
   ↓
C#
   ↓
ADO.NET
   ↓
Stored Procedure
   ↓
SQL Server
   ↓
Database
```

### Most important mental model

> **A stored procedure is essentially a reusable database-side method that your ASP.NET application can call.**
