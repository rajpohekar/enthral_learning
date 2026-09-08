# ASP.NET Web Forms — Revision Notes
*Consolidated from `enthral_learning` repo (21–27 Aug 2026)*

---

## 1. What Is ASP.NET Web Forms?
An event-driven Microsoft web framework (built on .NET Framework) based on **pages, server controls, postbacks, state management, and a page lifecycle**. It doesn't introduce a new language — it uses **C#** for code-behind.

```
Employee.aspx        → UI / server controls (markup)
Employee.aspx.cs      → C# code-behind (server-side logic)
Web.config            → application configuration
Site.Master           → Master Page (shared layout), when used
```
`runat="server"` marks a control as server-side so C# can access it (e.g. `txtName.Text`).

**Full request flow:**
```
Browser → HTTP Request → ASP.NET → Page Lifecycle → ViewState/Postback Data
→ Page_Load → Control Events → Validation/Logic → C# Code-Behind → Render → HTML Response → Browser
```

---

## 2. Page Lifecycle
```
Request → Init → Load ViewState → Load PostBack Data → Page_Load
→ Control Events → Validation → PreRender → Save ViewState → Render → Response
```
- **Init** — prepare/initialize page & controls.
- **Page_Load** — runs on **both** the first request *and* every postback (hence `IsPostBack` matters).
- **PreRender** — last chance to modify the page before rendering.
- **Render** — server controls converted to plain HTML the browser understands.

**The most important pattern in Web Forms:**
```csharp
protected void Page_Load(object sender, EventArgs e)
{
    if (!IsPostBack)
    {
        LoadData();   // run only on first load, not every postback
    }
}
```
> `IsPostBack == false` → first request. `IsPostBack == true` → postback. Without the check, e.g. a dropdown gets re-bound on every postback, wiping the user's selection.

---

## 3. PostBack

A **PostBack** = the page sending a request back to the server, usually from a server control interaction (e.g. Button click).
```
User clicks Save → POST request/postback → ASP.NET processes page
→ Page lifecycle runs → Button event raised → btnSave_Click() → C# executes
```
`IsPostBack` tells you whether this is the initial load or a postback.

---

## 4. State Management: ViewState vs Session vs Application State

| Feature | Scope | Typical Use | Storage |
|---|---|---|---|
| **ViewState** | Page/control | Preserve control state across postbacks | Hidden field `__VIEWSTATE`, carried **in the page** (not DB) |
| **SessionState** | One user's session | User-specific data | Server-side (default `InProc`) |
| **ApplicationState** | Entire application | Shared/global data | Server-side, shared by all users |

```csharp
ViewState["Message"] = "Data Saved";           string msg = ViewState["Message"].ToString();
Session["UserId"] = 101;                        int id = (int)Session["UserId"];
Application["TotalVisitors"] = 100;             int count = (int)Application["TotalVisitors"];
```

**Session config (`web.config`):**
```xml
<sessionState mode="InProc" timeout="20" />
```
Timeout = minutes of **inactivity** (not from login) before expiry; activity resets the clock.

**Session State Modes:**

| Mode | Storage | Speed | Survives worker restart | Web Farm friendly |
|---|---|---|---|---|
| `Off` | disabled | — | — | — |
| `InProc` (default) | worker-process memory | fastest | ❌ | ❌ |
| `StateServer` | ASP.NET State Service | medium | ✅ | ✅ |
| `SQLServer` | SQL Server DB | slower | ✅ | ✅ |
| `Custom` | custom provider | depends | depends | depends |

> Progression: **InProc** (fast, single server, no restart-safety) → **StateServer** (external, restart-safe, needs serialization) → **SQLServer** (DB-backed, best for Web Farms, most overhead).

**Application State thread safety** — shared across all requests, so guard writes:
```csharp
Application.Lock();
Application["Count"] = (int)Application["Count"] + 1;
Application.UnLock();
```

**Disabling session:**
```aspx
<%@ Page EnableSessionState="false" %>      <!-- page level -->
```
```xml
<sessionState mode="Off" />                  <!-- app level -->
```

**Cookieless sessions** — session ID embedded in the URL instead of a cookie (`cookieless="true"`); use relative redirects (`Response.Redirect("~/Page.aspx")`).

**Session vs Cookie:** Cookie = client-side storage; Session data = server-side (though the *session ID itself* is normally stored in a cookie).

---

## 5. Query Strings & Cookies

**Query String** — name/value pairs on the URL: `WebForm2.aspx?id=101&name=Raj`
```csharp
Response.Redirect("WebForm2.aspx?id=101&name=Raj");
string id = Request.QueryString["id"];
```
Visible in URL, length-limited — not for large/sensitive data.

**Cookies** — small client-side data.
```csharp
HttpCookie cookie = new HttpCookie("UserName") { Value = "Raj", Expires = DateTime.Now.AddDays(7) };
Response.Cookies.Add(cookie);                                  // persistent (has Expires)
if (Request.Cookies["UserName"] != null) { ... .Value; }        // non-persistent = no Expires, session-only
```

**Choosing a data-transfer technique:**
| Need | Use |
|---|---|
| Small, non-sensitive, page→page | Query String |
| Client preference (e.g. theme) | Cookie |
| User-specific server data | Session |
| Data during Server.Transfer | Context.Handler |
| Form → another form | Cross-Page PostBack |
| Global, all-users data | Application State |

---

## 6. Server Controls Cheat Sheet

| Control | Key properties/events |
|---|---|
| **TextBox** | `Text`, `TextMode` (SingleLine/MultiLine/Password), `MaxLength`, `ReadOnly`, `Rows`/`Columns`, `AutoPostBack`, `TextChanged`, `Focus()` |
| **RadioButton** | `Checked`, `Text`, `TextAlign`, `GroupName` (same group ⇒ mutually exclusive), `AutoPostBack`, `CheckedChanged` |
| **CheckBox** | `Checked`, `Text`, `TextAlign`, `AutoPostBack`, `CheckedChanged`, `Focus()` — independently selectable (unlike RadioButton) |
| **HyperLink** | `Text`, `NavigateUrl`, `ImageUrl`, `Target` — pure navigation, **no server Click event** |
| **Button / LinkButton / ImageButton** | Normal / link-styled / image-based; all raise `Click` and `Command` |
| **DropDownList** | `Items` (`ListItem`s), `SelectedValue`, `SelectedItem`, `DataSource`, `DataTextField`, `DataValueField`, `DataBind()` |
| **GridView** | `DataSource`, `DataBind()` — tabular display, supports sorting/paging/edit/delete |

**AutoPostBack** — causes the control to postback immediately when its value changes (rather than waiting for a Save button click).

**Click vs Command events:**
```csharp
// Click — one control, one dedicated action
protected void btnSave_Click(object sender, EventArgs e) { }

// Command — one shared handler for many controls, using CommandName + CommandArgument
protected void Button_Command(object sender, CommandEventArgs e)
{
    if (e.CommandName == "Delete") { DeleteEmployee(e.CommandArgument.ToString()); }
}
```
> Event order when both fire: **Click → Command**. Command is ideal for GridView row-level Edit/Delete buttons sharing one handler.

**DropDownList data binding (DataTextField vs DataValueField):**
```csharp
ddlCountry.DataSource = dt;               // e.g. from DataSet.ReadXml() via Server.MapPath()
ddlCountry.DataTextField = "CountryName"; // what the USER sees
ddlCountry.DataValueField = "CountryId";  // what the APP uses
ddlCountry.DataBind();
ddlCountry.Items.Insert(0, new ListItem("Select", "0"));   // add a placeholder at top
```
`Server.MapPath("~/Data/Countries.xml")` converts a virtual path to a physical server path.

---

## 7. DataBinding & GridView

```csharp
DataTable dt = GetEmployees();
GridView1.DataSource = dt;
GridView1.DataBind();
```
`DataSource` = *here's the data*; `DataBind()` = *now render it into the control*.

**Full flow:** `Database → DataTable → DataSource → DataBind() → GridView → Render → Browser`

**Typical page pattern:**
```csharp
protected void Page_Load(object sender, EventArgs e)
{
    if (!IsPostBack) LoadEmployees();
}
private void LoadEmployees()
{
    DataTable dt = GetEmployees();
    GridView1.DataSource = dt;
    GridView1.DataBind();
}
```

---

## 8. Validation Controls

**6 built-in validators**, each can do **client-side and server-side** validation (client-side can be bypassed — always also validate server-side):

| Validator | Purpose | Key property |
|---|---|---|
| `RequiredFieldValidator` | value must be entered | `ControlToValidate`, `InitialValue` (for dropdowns) |
| `RangeValidator` | value within a min/max | `Type`, `MinimumValue`, `MaximumValue` |
| `CompareValidator` | compare 2 controls / a constant / check data type | `ControlToCompare`, `Operator`, `ValueToCompare`, `Type` |
| `RegularExpressionValidator` | format/pattern match (email, phone, etc.) | `ValidationExpression` (checks *format* only, not existence) |
| `CustomValidator` | custom business rule not covered by built-ins | `OnServerValidate`, `ClientValidationFunction` |
| `ValidationSummary` | aggregate all error messages in one place | `HeaderText`, `ShowSummary`, `ShowMessageBox`, `DisplayMode` |

**Common properties:** `ControlToValidate`, `ErrorMessage`, `Text`, `Display` (`None`/`Static`/`Dynamic`), `SetFocusOnError`, `Enabled`, `EnableClientScript`, `ValidationGroup`.

```aspx
<asp:CompareValidator
    ControlToValidate="txtConfirmPassword"
    ControlToCompare="txtPassword"
    Operator="Equal" Type="String"
    ErrorMessage="Passwords must match" />
```

**Display modes:**
| Value | Behavior |
|---|---|
| `None` | no inline message (rely on ValidationSummary) |
| `Static` | space reserved even when valid (`visibility:hidden`) |
| `Dynamic` | no space reserved until an error appears (`display:none`) |

**ValidationGroup** — isolates validation per form-section so, e.g., clicking **Login** doesn't validate the **Register** fields:
```aspx
<asp:Button ID="btnLogin" ValidationGroup="LoginGroup" ... />
```
**CausesValidation="false"** — lets a button (e.g. Clear) skip validation entirely.

**Server-side check:**
```csharp
protected void btnSave_Click(object sender, EventArgs e)
{
    if (!Page.IsValid) return;
    // save data
}
```

---

## 9. Page Navigation Techniques

| Technique | URL changes? | New browser request? | Same server only? | Form data | Current page continues? |
|---|---|---|---|---|---|
| `HyperLink` | Yes | Yes | No | normal request | No |
| `Response.Redirect()` | Yes | Yes (2 cycles) | No | new request | No |
| `Server.Transfer()` | No | No (1 cycle) | Yes | preserved | No (ends) |
| `Server.Execute()` | No | No | Yes | preserved | Yes (returns after) |
| Cross-Page PostBack (`PostBackUrl`) | Yes | Yes | app-level | posted data | No |
| `window.open()` (JS) | new tab | Yes | No | via URL | Yes |

```csharp
Response.Redirect("WebForm2.aspx");   // browser redirect, new GET, URL changes, slower
Server.Transfer("WebForm2.aspx");     // server-side transfer, URL unchanged, faster, form vars preserved
Server.Execute("WebForm2.aspx");      // runs WebForm2 then RETURNS to WebForm1 (Transfer ends WebForm1)
```
> Memory trick: **Response.Redirect → browser redirects, new request.** **Server.Transfer → server transfers, same request.**

**Cross-Page PostBack:**
```aspx
<asp:Button ID="btnSubmit" runat="server" Text="Submit" PostBackUrl="WebForm2.aspx" />
```
Destination page reads the source page via `Page.PreviousPage`:
```csharp
Page previousPage = Page.PreviousPage;
TextBox txtName = (TextBox)previousPage.FindControl("txtName");   // risk: null if ID misspelled → NullReferenceException
```
**Safer approach** — expose a public property on the source page instead of `FindControl()`:
```csharp
// Source page: public string EmployeeName => txtName.Text;
WebForm1 prev = (WebForm1)Page.PreviousPage;
lblName.Text = prev.EmployeeName;
```

**`Context.Handler`** — accesses the previous page during `Server.Transfer()`/`Server.Execute()`; not permanent (changes if the destination page posts back to itself).

---

## 10. ADO.NET & SQL Server

**Core classes:** `SqlConnection`, `SqlCommand`, `SqlDataReader`, `DataTable`, `DataSet`, `SqlDataAdapter`.

```csharp
using (SqlConnection con = new SqlConnection(connectionString))
{
    SqlCommand cmd = new SqlCommand("SELECT Name FROM Employee", con);
    con.Open();
    SqlDataReader reader = cmd.ExecuteReader();
    while (reader.Read()) { string name = reader["Name"].ToString(); }
}
```
- **SqlDataReader** — reads results sequentially (forward-only).
- **DataTable** — holds tabular data in memory; can bind directly to `GridView`.

**Parameterized SQL (prevents SQL injection):**
```csharp
cmd.Parameters.AddWithValue("@Id", id);     // ok
cmd.Parameters.Add("@Name", SqlDbType.VarChar, 100).Value = name;  // preferred (typed)
```
> Note: using a stored procedure does **not** automatically prevent SQL injection — unsafe *dynamic SQL inside* the procedure can still be vulnerable.

---

## 11. Stored Procedures

A stored procedure = a **named, reusable SQL program stored inside the database** (like a C# method, but living in SQL Server).

```sql
CREATE PROCEDURE GetEmployeeById
    @EmployeeId INT
AS
BEGIN
    SELECT * FROM Employees WHERE ID = @EmployeeId;
END
```
```sql
EXEC GetEmployeeById 2;
```

**Calling from C#:**
```csharp
SqlCommand cmd = new SqlCommand("GetEmployeeById", con);
cmd.CommandType = CommandType.StoredProcedure;
cmd.Parameters.AddWithValue("@EmployeeId", 2);
```

**CRUD set:** `GetEmployees`, `GetEmployeeById`, `InsertEmployee`, `UpdateEmployee`, `DeleteEmployee`.

**Ways a procedure returns data:**
1. Result set (`SELECT`)
2. Output parameter (`@Count INT OUTPUT`)
3. `RETURN` value (status/int code)

**Why companies use them:** centralized DB logic, reusability, security (limit direct table access), maintainability, and plan-caching benefits.
> **Misconception to avoid:** "stored procedure = automatically faster." Performance still depends on indexes, execution plans, query design.

**Full architecture:**
```
ASPX → aspx.cs → C#/ADO.NET → SqlConnection → SqlCommand → Stored Procedure
→ SQL Server → Database Tables → DataTable → GridView → Browser
```

---

## 12. User Controls & Custom Events

**User Control (`.ascx`)** — a reusable bundle of controls/markup/code (e.g. a `CalendarUserControl` combining a TextBox + ImageButton + Calendar), used to avoid repeating the same UI/logic across multiple pages. Benefits: reusability, encapsulation, less duplication, easier maintenance/testing.

**Custom event — 5-step pattern** (User Control notifies its parent page, e.g. calendar visibility changed):
1. Create a custom `EventArgs` class carrying the relevant data.
2. Create a delegate defining the handler signature.
3. Declare the event using that delegate.
4. Create a `protected virtual On<EventName>()` method that raises it (`Event?.Invoke(this, e);`).
5. Call that method wherever the state actually changes.

**Consuming it in the parent page:**
```csharp
CalendarUserControl1.CalendarVisibilityChanged += CalendarUserControl1_CalendarVisibilityChanged;
```
> The User Control is the **publisher**; the parent page is the **subscriber** — this keeps the control decoupled from any specific page.

**Loading User Controls dynamically** — useful when the needed control depends on a runtime condition (e.g. admin vs non-admin). Load into a `PlaceHolder`/`Panel`; recreate consistently on every postback (commonly in `Page_Init()`) so ASP.NET can restore state and rewire events correctly.

---

## 13. Interview-Style Quick Answers

- **What is PostBack?** Page sends a request back to the server, usually from user interaction with a server control.
- **Why `if (!IsPostBack)`?** Run init/data-load code only on first load, not on every postback.
- **ViewState vs Session vs Application?** Page/control scope vs one user's session vs entire app, shared by everyone.
- **Response.Redirect vs Server.Transfer?** Redirect = browser-side, 2 request cycles, URL changes; Transfer = server-side, 1 cycle, URL unchanged, faster, same-server only.
- **Why server-side validation if client-side exists?** Client-side JS can be disabled/bypassed.
- **DataTextField vs DataValueField?** What the user sees vs what the app uses internally.
- **Why does Command exist alongside Click?** One shared handler can process many controls using `CommandName` + `CommandArgument` (e.g. GridView row actions).
- **Problem with FindControl()?** Returns `null` if ID is wrong → `NullReferenceException`; prefer strongly-typed public properties.
- **Does a stored procedure guarantee protection from SQL injection?** No — only parameterization does; unsafe dynamic SQL inside a procedure is still vulnerable.

---

## 14. End-to-End Mental Model

```
                 WEB FORMS PAGE
                       │
            ┌──────────┴──────────┐
          .aspx                .aspx.cs
       (UI/Controls)          (C# Logic)
            └──────────┬──────────┘
                       ↓
                Page Lifecycle
     (Init → Page_Load[IsPostBack] → Events → PreRender → Render)
                       ↓
                  Data Layer
       (ADO.NET → Stored Procedure → SQL Server)
                       ↓
                  DataTable → DataBinding → GridView
                       ↓
                    Browser
```

---

## 15. Beyond the Learning Repo — What Matters for a Real Project Like CCMS

The repo's daily notes stop at User Controls/custom events (27 Aug). A **Currency Chest Management System** is a financial, multi-role, audit-sensitive application, so a few production topics are worth knowing even though they weren't in the day-by-day log yet.

### 15.1 Master Pages (shared layout)
Almost every real Web Forms app uses a Master Page instead of repeating header/menu/footer on every `.aspx`.
```aspx
<%-- Site.Master --%>
<asp:ContentPlaceHolder ID="MainContent" runat="server" />
```
```aspx
<%-- ChestBalance.aspx --%>
<%@ Page MasterPageFile="~/Site.Master" %>
<asp:Content ContentPlaceHolderID="MainContent" runat="server">
    <!-- page-specific UI -->
</asp:Content>
```
Access master-page controls from a content page via `Master.FindControl("...")` or a strongly-typed public property on the master, same idea as `Page.PreviousPage`.

### 15.2 GridView in Depth (editing, paging, sorting, templates)
The notes cover basic binding; a real chest-transaction grid usually needs:
```aspx
<asp:GridView ID="gvChestTxn" runat="server" AutoGenerateColumns="false"
    AllowPaging="true" PageSize="20" OnPageIndexChanging="gvChestTxn_PageIndexChanging"
    AllowSorting="true" OnSorting="gvChestTxn_Sorting"
    OnRowCommand="gvChestTxn_RowCommand" OnRowDataBound="gvChestTxn_RowDataBound">
    <Columns>
        <asp:BoundField DataField="TxnDate" HeaderText="Date" DataFormatString="{0:dd-MM-yyyy}" />
        <asp:BoundField DataField="Amount" HeaderText="Amount (Rs.)" DataFormatString="{0:N2}" />
        <asp:TemplateField HeaderText="Action">
            <ItemTemplate>
                <asp:LinkButton runat="server" CommandName="Approve"
                    CommandArgument='<%# Eval("TxnId") %>' Text="Approve" />
            </ItemTemplate>
        </asp:TemplateField>
    </Columns>
</asp:GridView>
```
```csharp
protected void gvChestTxn_PageIndexChanging(object sender, GridViewPageEventArgs e)
{
    gvChestTxn.PageIndex = e.NewPageIndex;
    BindGrid();
}
protected void gvChestTxn_RowCommand(object sender, GridViewCommandEventArgs e)
{
    if (e.CommandName == "Approve")
    {
        int txnId = Convert.ToInt32(e.CommandArgument);
        ApproveTransaction(txnId);
    }
}
protected void gvChestTxn_RowDataBound(object sender, GridViewRowEventArgs e)
{
    if (e.Row.RowType == DataControlRowType.DataRow)
    {
        // e.g. color a row red if balance is negative / flagged
    }
}
```
Key ideas: `AutoGenerateColumns="false"` + `BoundField`/`TemplateField` for controlled columns and formatting; **rebind the grid on every relevant postback** (paging/sorting/editing all need `BindGrid()` called again since GridView doesn't retain its data source between postbacks); `RowCommand` + `CommandName`/`CommandArgument` is the standard pattern for per-row actions (edit/approve/reject a chest transaction).

**Repeater / DataList** — lighter-weight alternatives to GridView when you need full template control and don't need built-in paging/sorting/editing (e.g. a dashboard summary of chest balances by branch).

### 15.3 Web.config Essentials
```xml
<connectionStrings>
    <add name="CCMSConnection"
         connectionString="Data Source=.;Initial Catalog=CCMS;Integrated Security=True" />
</connectionStrings>
<appSettings>
    <add key="MaxChestLimit" value="500000" />
</appSettings>
<system.web>
    <compilation debug="false" targetFramework="4.8" />
    <customErrors mode="On" defaultRedirect="~/Error.aspx" />
    <authentication mode="Forms">
        <forms loginUrl="~/Login.aspx" timeout="20" />
    </authentication>
</system.web>
```
Reading these in code:
```csharp
string connStr = ConfigurationManager.ConnectionStrings["CCMSConnection"].ConnectionString;
string limit = ConfigurationManager.AppSettings["MaxChestLimit"];
```
`debug="false"` and `customErrors mode="On"` matter for production — you don't want stack traces shown to end users of a banking-adjacent system.

### 15.4 Global.asax — Application-Level Events
```csharp
protected void Application_Start(object sender, EventArgs e) { /* app init, e.g. warm caches */ }
protected void Session_Start(object sender, EventArgs e) { /* per-user session init */ }
protected void Application_Error(object sender, EventArgs e)
{
    Exception ex = Server.GetLastError();
    // central logging here — very important for an auditable financial system
}
protected void Session_End(object sender, EventArgs e) { }
```
`Application_Error` is the standard place for **centralized exception logging** rather than scattering try/catch-and-log everywhere.

### 15.5 Authentication & Authorization (Roles)
CCMS almost certainly has different user types (e.g. chest custodian, verifier/approver, admin) — this needs Forms Authentication + role checks, not just a login form:
```csharp
// After validating username/password against the Users table:
FormsAuthentication.SetAuthCookie(username, false);
```
```xml
<location path="Admin">
    <system.web>
        <authorization>
            <allow roles="Admin" />
            <deny users="*" />
        </authorization>
    </system.web>
</location>
```
Role check in code-behind:
```csharp
if (!User.IsInRole("Approver"))
{
    Response.Redirect("~/AccessDenied.aspx");
}
```
This is the natural next layer on top of Session (which only tells you *who* is logged in, not *what they're allowed to do*).

### 15.6 Database Transactions (critical for money-moving operations)
Any operation that touches chest balances (deposit, withdrawal, transfer between chests) should be atomic — either the whole operation succeeds or none of it does.
```csharp
using (SqlConnection con = new SqlConnection(connStr))
{
    con.Open();
    SqlTransaction txn = con.BeginTransaction();
    try
    {
        SqlCommand cmd1 = new SqlCommand("UPDATE Chest SET Balance = Balance - @Amt WHERE ChestId=@From", con, txn);
        SqlCommand cmd2 = new SqlCommand("UPDATE Chest SET Balance = Balance + @Amt WHERE ChestId=@To", con, txn);
        // set parameters, ExecuteNonQuery() both
        txn.Commit();
    }
    catch
    {
        txn.Rollback();
        throw;
    }
}
```
Equivalent SQL-side pattern inside a stored procedure:
```sql
BEGIN TRY
    BEGIN TRANSACTION;
        UPDATE Chest SET Balance = Balance - @Amt WHERE ChestId = @From;
        UPDATE Chest SET Balance = Balance + @Amt WHERE ChestId = @To;
    COMMIT TRANSACTION;
END TRY
BEGIN CATCH
    ROLLBACK TRANSACTION;
    THROW;
END CATCH
```
> This is one of the most important gaps to fill for CCMS specifically — a currency chest system without transaction-safe balance updates is a data-integrity risk.

### 15.7 Currency, Decimal Precision & Culture Formatting
- Always use `decimal` (not `float`/`double`) for money — matches the earlier C# note on `decimal` for financial calculations.
- Format for display: `amount.ToString("N2")` or `amount.ToString("C", new CultureInfo("en-IN"))` for Indian-Rupee-style grouping.
- Be deliberate about rounding (`Math.Round(amount, 2, MidpointRounding.AwayFromZero)`), since silent rounding differences are a classic source of reconciliation bugs in financial systems.

### 15.8 Exporting Data (Excel/PDF) — common reporting need
```csharp
Response.ClearContent();
Response.Buffer = true;
Response.AddHeader("content-disposition", "attachment;filename=ChestReport.xls");
Response.ContentType = "application/vnd.ms-excel";
StringWriter sw = new StringWriter();
HtmlTextWriter htw = new HtmlTextWriter(sw);
GridView1.RenderControl(htw);
Response.Write(sw.ToString());
Response.End();
```
Chest audit/reconciliation reports are a very likely CCMS feature — worth knowing at least the GridView-to-Excel export pattern above.

### 15.9 Partial-Page Updates (AJAX in Web Forms)
Full postbacks reload the whole page; `UpdatePanel` avoids that for smoother UX (e.g. refreshing a balance figure without a full page flicker):
```aspx
<asp:ScriptManager runat="server" />
<asp:UpdatePanel runat="server">
    <ContentTemplate>
        <asp:Label ID="lblBalance" runat="server" />
        <asp:Button ID="btnRefresh" runat="server" Text="Refresh" OnClick="btnRefresh_Click" />
    </ContentTemplate>
</asp:UpdatePanel>
```
The code-behind event handler (`btnRefresh_Click`) works exactly like a normal postback handler — `UpdatePanel` just suppresses the full-page render on the client.

### 15.10 Security Checklist Beyond Parameterized SQL
- **ViewState MAC** — leave `EnableViewStateMac` on (default) to stop ViewState tampering.
- **Input validation** on every field that reaches a query or business rule, even if a validator control already checked it client-side (defense in depth — same principle as "always also validate server-side").
- **Least-privilege DB account** for the app's connection string (should not be `sa`).
- **Audit columns** on financial tables — `CreatedBy`, `CreatedDate`, `ModifiedBy`, `ModifiedDate` (and often a separate `AuditLog` table) so every chest-balance change is traceable to a user and timestamp — standard practice in banking-adjacent systems and worth asking about if the CCMS schema doesn't already have it.

### 15.11 Where This Slots Into the Existing Mental Model
```
Master Page (shared layout)
       |
Web Forms Page (Auth/Role-checked)
       |
Validation Controls -> GridView (paging/sorting/RowCommand)
       |
C# Code-Behind
       |
Service/Repository layer (from the SOLID/DI notes)
       |
ADO.NET + SqlTransaction  <- atomic, money-safe
       |
Stored Procedure (with TRY/CATCH + TRANSACTION)
       |
SQL Server (audit columns, decimal precision)
       |
Global.asax Application_Error <- catches anything that slips through
```
