# ASP.NET Web Forms Preparation

## Topics Covered --- 21/08/2026

1.  ASP.NET Basics
2.  Creating a Web Forms Application
3.  Web Forms Application Structure
4.  ASPX and Code-Behind
5.  ASP.NET Page Lifecycle
6.  ViewState
7.  SessionState
8.  ApplicationState
9.  Postback
10. IsPostBack
11. Server Controls
12. Server Control Events
13. Postback Events
14. Cached Events
15. Validation Events
16. C# Code-Behind and Event Handling

------------------------------------------------------------------------

# 1. ASP.NET Basics

ASP.NET is Microsoft's web application framework for building web
applications using the .NET ecosystem.

ASP.NET Web Forms is an event-driven web application framework based on
pages, server controls, postbacks, state management, and a page
lifecycle.

------------------------------------------------------------------------

# 2. Creating a Web Forms Application

A Web Forms application typically contains:

``` text
.aspx       → Web Forms page / UI markup
.aspx.cs    → C# code-behind
```

The `.aspx` file defines the page and server controls.

The `.aspx.cs` file contains server-side C# logic.

------------------------------------------------------------------------

# 3. Web Forms Application Structure

A simplified structure can look like:

``` text
Web Application
│
├── EmployeeMaster.aspx
├── EmployeeMaster.aspx.cs
├── Site.Master
├── Web.config
└── Other application files
```

Important files:

-   `.aspx` → page markup
-   `.aspx.cs` → code-behind
-   `Web.config` → application configuration
-   `.master` → Master Page, when used

------------------------------------------------------------------------

# 4. ASPX and C# Code-Behind

Example server control:

``` aspx
<asp:TextBox ID="txtName" runat="server" />
```

The corresponding C# code-behind can access it:

``` csharp
string name = txtName.Text;
```

The relationship is:

``` text
.aspx
  ↓
UI / server controls
  ↓
.aspx.cs
  ↓
C# code-behind
```

## `runat="server"`

A Web Forms control normally needs `runat="server"` to be treated as a
server-side control that can be accessed from server-side code.

Example:

``` aspx
<asp:Button
    ID="btnSave"
    runat="server"
    Text="Save"
    OnClick="btnSave_Click" />
```

------------------------------------------------------------------------

# 5. ASP.NET Page Lifecycle

A Web Forms page goes through a defined lifecycle during a request.

A simplified flow is:

``` text
Request
  ↓
Initialization
  ↓
Load ViewState
  ↓
Load Postback Data
  ↓
Page_Load
  ↓
Control Events
  ↓
Validation
  ↓
PreRender
  ↓
Render
  ↓
Response
```

The exact lifecycle contains more stages, but understanding the major
stages is the important foundation.

## `Page_Load`

Example:

``` csharp
protected void Page_Load(object sender, EventArgs e)
{
}
```

`Page_Load` runs during the page's Load stage.

It can run during the initial request and during postbacks.

------------------------------------------------------------------------

# 6. `IsPostBack`

Because `Page_Load` runs on both initial requests and postbacks,
`IsPostBack` is commonly used to distinguish them.

``` csharp
protected void Page_Load(object sender, EventArgs e)
{
    if (!IsPostBack)
    {
        LoadEmployees();
    }
}
```

Meaning:

``` text
First page request
→ IsPostBack = false

Postback request
→ IsPostBack = true
```

This is commonly used to prevent initial data-loading logic from
executing again unnecessarily on every postback.

------------------------------------------------------------------------

# 7. ViewState

HTTP is stateless.

ViewState helps ASP.NET preserve page/control state across postbacks.

Conceptually:

``` text
Initial Request
      ↓
Page state
      ↓
ViewState
      ↓
Postback
      ↓
State restored
```

ViewState is maintained by the page and is typically transported to the
client in hidden form data.

Important:

> ViewState is different from SessionState.

ViewState is primarily concerned with preserving page/control state
across postbacks.

------------------------------------------------------------------------

# 8. SessionState

SessionState stores information associated with a particular user's
session.

Example:

``` csharp
Session["UserName"] = "Raj";
```

Later:

``` csharp
string name = Session["UserName"].ToString();
```

Mental model:

``` text
ViewState
→ Page/control state

SessionState
→ User/session-specific state
```

------------------------------------------------------------------------

# 9. ApplicationState

ApplicationState stores data associated with the application as a whole.

Example:

``` csharp
Application["CompanyName"] = "Enthral";
```

It is shared across users of the application.

Mental model:

``` text
ViewState
→ Page

SessionState
→ User/session

ApplicationState
→ Entire application
```

------------------------------------------------------------------------

# 10. Postback

A postback occurs when a Web Forms page sends a request back to the
server, commonly as a result of interaction with a server control.

Example:

``` aspx
<asp:Button
    ID="btnSave"
    runat="server"
    Text="Save"
    OnClick="btnSave_Click" />
```

When the user clicks the button:

``` text
User clicks Save
      ↓
POST request / postback
      ↓
ASP.NET processes the page
      ↓
Page lifecycle runs
      ↓
Button event is raised
      ↓
btnSave_Click()
      ↓
C# code executes
```

------------------------------------------------------------------------

# 11. Server Controls

ASP.NET Web Forms provides server controls that are processed on the
server.

Examples:

``` aspx
<asp:TextBox ID="txtName" runat="server" />

<asp:Label ID="lblMessage" runat="server" />

<asp:Button ID="btnSave" runat="server" Text="Save" />

<asp:GridView ID="gvEmployees" runat="server" />
```

These controls expose properties, methods, and events to server-side C#.

------------------------------------------------------------------------

# 12. Server Control Events

Web Forms is event-driven.

Example:

``` aspx
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
    string name = txtName.Text.Trim();
}
```

The flow is:

``` text
User action
    ↓
Postback
    ↓
Page lifecycle
    ↓
Control event
    ↓
C# event handler
```

------------------------------------------------------------------------

# 13. Postback Events

Postback events are events caused by user interaction that result in a
postback.

Examples include:

-   Button click
-   LinkButton click
-   SelectedIndexChanged for controls configured to post back
-   Other control-specific postback events

Example:

``` csharp
protected void btnSave_Click(object sender, EventArgs e)
{
    // Server-side logic
}
```

------------------------------------------------------------------------

# 14. Cached Events

Some Web Forms events are not necessarily raised immediately as the
control interaction occurs. They can be accumulated and processed during
the appropriate lifecycle stage.

For practical development, the important point is to understand that Web
Forms manages event ordering as part of the page lifecycle rather than
treating every browser interaction as a direct method call.

------------------------------------------------------------------------

# 15. Validation Events

Web Forms provides validation controls and validation-related
processing.

Examples of validation controls include:

``` aspx
<asp:RequiredFieldValidator
    ID="rfvName"
    runat="server"
    ControlToValidate="txtName"
    ErrorMessage="Name is required" />
```

Validation helps ensure that input satisfies defined rules before
server-side processing continues.

Server-side code can also check:

``` csharp
if (Page.IsValid)
{
    // Process valid input
}
```

------------------------------------------------------------------------

# 16. C# Code-Behind and Event Handling

A typical Web Forms page connects markup to C# code-behind.

### ASPX

``` aspx
<asp:TextBox ID="txtName" runat="server" />

<asp:Button
    ID="btnSave"
    runat="server"
    Text="Save"
    OnClick="btnSave_Click" />
```

### Code-behind

``` csharp
protected void btnSave_Click(object sender, EventArgs e)
{
    string name = txtName.Text.Trim();

    if (name.Length > 0)
    {
        // Process employee
    }
}
```

Here:

``` text
txtName
 ↓
Server-side TextBox control

.Text
 ↓
User-entered value

.Trim()
 ↓
Remove leading/trailing whitespace

name
 ↓
C# string variable
```

------------------------------------------------------------------------

# Complete Web Forms Request Flow

The most important mental model is:

``` text
                    Browser
                       ↓
                  HTTP Request
                       ↓
                    ASP.NET
                       ↓
                Page Lifecycle
                       ↓
             ViewState / Postback Data
                       ↓
                  Page_Load
                       ↓
                Control Events
                       ↓
              Validation / Logic
                       ↓
                C# Code-Behind
                       ↓
                    Render
                       ↓
                 HTTP Response
                       ↓
                    Browser
```

------------------------------------------------------------------------

# ViewState vs SessionState vs ApplicationState

  -----------------------------------------------------------------------
  Feature                 Scope                   Typical Purpose
  ----------------------- ----------------------- -----------------------
  ViewState               Page/control            Preserve page/control
                                                  state across postbacks

  SessionState            Individual user/session Store user-specific
                                                  session data

  ApplicationState        Entire application      Store application-wide
                                                  data
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# Web Forms Revision Checklist

-   [ ] Explain what ASP.NET Web Forms is
-   [ ] Explain `.aspx` and `.aspx.cs`
-   [ ] Explain code-behind
-   [ ] Understand `runat="server"`
-   [ ] Understand server controls
-   [ ] Explain the major page lifecycle stages
-   [ ] Understand `Page_Load`
-   [ ] Understand `IsPostBack`
-   [ ] Explain ViewState
-   [ ] Explain SessionState
-   [ ] Explain ApplicationState
-   [ ] Explain postback
-   [ ] Explain server control events
-   [ ] Explain validation
-   [ ] Explain how a button click reaches C# code-behind
