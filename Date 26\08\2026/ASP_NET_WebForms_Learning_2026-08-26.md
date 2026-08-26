# ASP.NET Web Forms Learning

## Date: 26/08/2026

### Topics Covered

1. Context.Handler
2. Query Strings
3. Cookies
4. Session State
5. Cookieless Sessions
6. Session State Modes
   - Off
   - InProc
   - StateServer
   - SQLServer
   - Custom
7. Application State

---

# 1. Context.Handler

`Context.Handler` can be used to retrieve data from a previous Web Form when navigating using:

- `Server.Transfer()`
- `Server.Execute()`

### Accessing the Previous Page

```csharp
WebForm1 previousPage = Context.Handler as WebForm1;
```

The previous page's controls can be accessed using `FindControl()` or, preferably, public properties.

### Important Points

- `Context.Handler` returns the previous page only when the request reaches the new page through `Server.Transfer()` or `Server.Execute()`.
- On a postback of the destination page, `Context.Handler` refers to the destination page rather than the original page.
- `FindControl()` can cause a runtime `NullReferenceException` if the control ID is incorrect.
- Public properties provide a safer and cleaner way to expose data from the previous page.

---

# 2. Query Strings

A query string is a collection of **name/value pairs** appended to a URL.

Example:

```text
WebForm2.aspx?id=101&name=Raj
```

### Syntax

- `?` starts the query string.
- `&` separates multiple parameters.

### Sending Data

```csharp
Response.Redirect("WebForm2.aspx?id=101&name=Raj");
```

### Reading Data

```csharp
string id = Request.QueryString["id"];
string name = Request.QueryString["name"];
```

### Important Points

- Query strings are commonly used to send data between Web Forms.
- Data is visible in the URL.
- Multiple query-string parameters can be used.
- Query strings have length limitations.
- They are not suitable for sending large or sensitive data.

---

# 3. Cookies

Cookies store small amounts of information on the **client's browser**.

They are commonly used for:

- User preferences
- Client-specific information
- Identifying a user/session

## Types of Cookies

### Persistent Cookie

A persistent cookie remains on the client after the browser is closed.

Its lifetime can be configured using the `Expires` property.

```csharp
HttpCookie cookie = new HttpCookie("UserName");
cookie.Value = "Raj";
cookie.Expires = DateTime.Now.AddDays(7);

Response.Cookies.Add(cookie);
```

### Non-Persistent Cookie

If the `Expires` property is not set, the cookie normally exists only for the browser session and is removed when the browser closes.

### Reading a Cookie

```csharp
if (Request.Cookies["UserName"] != null)
{
    string name = Request.Cookies["UserName"].Value;
}
```

---

# 4. Checking Whether Cookies Are Enabled

A common technique is:

1. Write a test cookie.
2. Redirect to the same page.
3. Read the test cookie.
4. If the cookie exists, cookies are enabled.
5. Otherwise, cookies are disabled.

This is useful because ASP.NET session management normally uses a cookie to store the session identifier.

---

# 5. Session State

Session state stores data for a **specific user's session**.

Example:

```csharp
Session["Name"] = "Raj";
```

Reading it:

```csharp
if (Session["Name"] != null)
{
    string name = Session["Name"].ToString();
}
```

### Important Characteristics

- Session variables are available across pages for the same session.
- Session state is stored on the server by default.
- The default session mode is `InProc`.
- Session lifetime is controlled by the `timeout` value in `web.config`.
- The default timeout is typically 20 minutes.

Example:

```xml
<sessionState mode="InProc" timeout="20" />
```

### Session Timeout

The timeout determines how long the session remains active without activity.

---

# 6. Disabling Session State

If a page does not need session state, it can be disabled to reduce unnecessary overhead.

### Page Level

```aspx
<%@ Page EnableSessionState="false" %>
```

### Application Level

```xml
<sessionState mode="Off" />
```

---

# 7. Cookieless Sessions

Normally, ASP.NET identifies a session using a session ID stored in a cookie.

With cookieless sessions, the session ID is included in the URL.

Example configuration:

```xml
<sessionState mode="InProc" cookieless="true" />
```

### Important Points

- The session ID becomes part of the URL.
- The server uses the session ID to identify the user's session.
- Relative URLs should be used when navigating between Web Forms.

Example:

```csharp
Response.Redirect("~/WebForm2.aspx");
```

Avoid hard-coded absolute URLs when working with cookieless sessions.

---

# 8. Session State Modes

ASP.NET session state supports these modes:

| Mode | Storage |
|---|---|
| `Off` | Session disabled |
| `InProc` | ASP.NET worker process memory |
| `StateServer` | ASP.NET State Service |
| `SQLServer` | SQL Server database |
| `Custom` | Custom session-state provider |

---

# 9. InProc Session State

`InProc` is the default session-state mode.

Session data is stored in the memory of the ASP.NET worker process.

```xml
<sessionState mode="InProc" timeout="20" />
```

### Advantages

- Very easy to configure.
- Fast because data is stored in local memory.
- Suitable for applications hosted on a single server.
- Objects can be stored without serialization.

### Disadvantages

- Session data is lost when the worker process/app pool is recycled.
- Not suitable for Web Farms.
- Can be problematic with Web Gardens.
- Scalability can become an issue.

### Best Use

Use `InProc` when the application is running on a **single server** and losing session data during a restart is acceptable.

---

# 10. StateServer Session State

With `StateServer`, session data is stored in the ASP.NET State Service instead of the worker process.

```xml
<sessionState
    mode="StateServer"
    stateConnectionString="tcpip=StateServer:42424"
    timeout="20" />
```

### Advantages

- Independent of the ASP.NET worker process.
- Session can survive worker-process restarts.
- Can be used with Web Farms and Web Gardens.
- More scalable than `InProc`.

### Disadvantages

- Slower than `InProc`.
- Complex objects need to be serializable.
- If the State Server itself goes down, sessions stored there can be lost.

---

# 11. SQLServer Session State

With `SQLServer`, session data is stored in a SQL Server database.

```xml
<sessionState
    mode="SQLServer"
    sqlConnectionString="data source=.;Integrated Security=SSPI"
    timeout="20" />
```

The ASP.NET session-state database must be configured before using this mode.

### Advantages

- Session data survives worker-process recycling.
- Suitable for Web Farms and Web Gardens.
- More scalable than `InProc` and `StateServer`.
- SQL Server provides persistent external storage.

### Disadvantages

- Slower than `InProc` and `StateServer`.
- Complex objects must be serialized/deserialized.
- Database access introduces additional overhead.

---

# 12. Application State

Application state stores data that is **shared across all users of the application**.

Example:

```csharp
Application["TotalVisitors"] = 100;
```

Reading:

```csharp
int count = (int)Application["TotalVisitors"];
```

### Session vs Application

| Feature | Session | Application |
|---|---|---|
| Scope | One user/session | Entire application |
| Shared between users | No | Yes |
| Lifetime | Session lifetime | Application lifetime |
| Example | Logged-in user's ID | Global configuration |
| Thread safety concern | Less global sharing | Important |

---

# 13. Application State Thread Safety

Application state is shared by multiple requests and users, so concurrent access can create race conditions.

Use `Lock()` and `UnLock()` when modifying shared application data.

```csharp
Application.Lock();

Application["GlobalVariable"] =
    (int)Application["GlobalVariable"] + 1;

Application.UnLock();
```

### Important

Application state should only be used when data genuinely needs:

- Global access
- Application-wide lifetime

For temporary global data, a cache can often be a better alternative.

---

# 14. Data Transfer Between Web Forms

Common techniques covered:

1. **Cross Page PostBack**
2. **Context.Handler**
3. **Query Strings**
4. **Cookies**
5. **Session State**
6. **Application State**

### Quick Comparison

| Technique | Stored Where? | Scope | URL Visible? |
|---|---|---|---|
| Context.Handler | Previous page/request context | Current navigation | No |
| Query String | URL/client | Request | Yes |
| Cookie | Client/browser | Client | No |
| Session | Server-side session store | One user | Usually session ID only |
| Application | Server/application | All users | No |

---

# 15. Interview Points

### Context.Handler

**Q: When is `Context.Handler` useful?**

When transferring/executing another Web Form using `Server.Transfer()` or `Server.Execute()` and data from the previous page needs to be accessed.

### Query String

**Q: Why shouldn't sensitive data be sent through query strings?**

Because query-string values are visible in the URL and may appear in browser history, logs, or other places.

### Cookies

**Q: Persistent vs non-persistent cookie?**

A persistent cookie has an expiry time and can survive browser restarts. A non-persistent cookie normally lasts only for the browser session.

### Session

**Q: What is the default ASP.NET session mode?**

`InProc`.

### InProc

**Q: What happens to InProc session data when the application pool recycles?**

The worker process memory is lost, so the session data is lost.

### StateServer

**Q: Why use StateServer?**

It moves session storage outside the ASP.NET worker process, making it more suitable for applications that need to survive worker-process restarts.

### SQLServer

**Q: Why use SQLServer session state?**

It stores session data in SQL Server and is suitable for scalable deployments such as Web Farms.

### Application State

**Q: What is the difference between Session and Application state?**

Session is user-specific; Application state is shared across the entire application.

---

# 16. Practical Tasks

- [ ] Create two Web Forms and pass data using `Context.Handler`.
- [ ] Pass Employee ID and Employee Name using a query string.
- [ ] Create and read a persistent cookie.
- [ ] Create and read a non-persistent cookie.
- [ ] Store Employee information in Session.
- [ ] Configure session timeout in `web.config`.
- [ ] Disable session state for a page.
- [ ] Test `InProc` session behavior.
- [ ] Understand `StateServer` configuration.
- [ ] Understand `SQLServer` session configuration.
- [ ] Store a global counter using Application State.
- [ ] Use `Application.Lock()` and `Application.UnLock()`.

---

# Key Takeaway

The main concept from today's learning is **ASP.NET Web Forms state management**.

Remember the scope:

```text
Context.Handler
      ↓
Current page navigation

Query String
      ↓
Data in URL

Cookie
      ↓
Data on client

Session
      ↓
Data for one user

Application
      ↓
Data shared by all users
```

For Session State:

```text
InProc
  ↓
StateServer
  ↓
SQLServer
  ↓
Custom
```

**Most important for practical Web Forms development:**  
`Session`, `Cookies`, `QueryString`, `Context.Handler`, `InProc`, `StateServer`, `SQLServer`, and `Application State`.


---

# Deep Dive: Techniques to Transfer Data Between Web Forms

ASP.NET Web Forms provides several ways to transfer data from one Web Form to another.

| Technique | Data Location | Best For | Data Visible to Client? |
|---|---|---|---|
| Cross-Page PostBack | Server/request | Form submission | No |
| `Context.Handler` | Current request | `Server.Transfer()` / `Server.Execute()` | No |
| Query String | URL | Small, non-sensitive data | Yes |
| Cookies | Client browser | Client preferences/small data | Yes |
| Session State | Server | User-specific data | Session ID only |
| Application State | Server | Global application data | No |

---

# 17. Cross-Page PostBack

Cross-page postback allows one Web Form to submit its form data directly to another Web Form.

### WebForm1.aspx

```aspx
<asp:Button
    ID="btnSubmit"
    runat="server"
    Text="Submit"
    PostBackUrl="~/WebForm2.aspx" />
```

Flow:

```text
WebForm1
   |
   | POST
   ↓
WebForm2
```

In WebForm2:

```csharp
if (PreviousPage != null)
{
    TextBox txtName =
        (TextBox)PreviousPage.FindControl("txtName");

    string name = txtName.Text;
}
```

A better approach is to expose a public property in WebForm1:

```csharp
public string EmployeeName
{
    get
    {
        return txtName.Text;
    }
}
```

Then:

```csharp
WebForm1 page = PreviousPage as WebForm1;

if (page != null)
{
    string name = page.EmployeeName;
}
```

### Advantages

- No data in the URL.
- Natural for form-to-form submission.
- Useful when one form directly submits to another.

### Disadvantages

- Primarily designed for form submission.
- Destination page needs knowledge of the source page.
- Creates coupling between the two pages.

---

# 18. Context.Handler

`Context.Handler` is useful when using:

```csharp
Server.Transfer("~/WebForm2.aspx");
```

or:

```csharp
Server.Execute("~/WebForm2.aspx");
```

Flow:

```text
Browser
   |
   | Request WebForm1
   ↓
WebForm1
   |
   | Server.Transfer()
   ↓
WebForm2
```

WebForm2 can access the previous handler:

```csharp
WebForm1 page = Context.Handler as WebForm1;
```

Then:

```csharp
string name = page.EmployeeName;
```

## Important Limitation

`Context.Handler` represents the handler associated with the current request.

Initial request:

```text
WebForm1
   |
   | Server.Transfer
   ↓
WebForm2
```

`Context.Handler` can refer to WebForm1.

But if WebForm2 performs a postback:

```text
WebForm2
   |
   | PostBack
   ↓
WebForm2
```

the current handler is WebForm2. Therefore, `Context.Handler` should not be treated as a permanent reference to WebForm1.

### `FindControl()` vs Public Properties

Using:

```csharp
TextBox txt =
    (TextBox)page.FindControl("txtName");
```

can cause problems if the control ID is incorrect or the control cannot be found.

A public property is cleaner:

```csharp
public string EmployeeName
{
    get { return txtName.Text; }
}
```

### When to Use

Use `Context.Handler` when:

- You control both pages.
- You use `Server.Transfer()` or `Server.Execute()`.
- Data is needed during the same request.

---

# 19. Query Strings

Query strings transfer data through the URL.

Example:

```text
WebForm2.aspx?id=101&name=Raj
```

### Sending

```csharp
Response.Redirect(
    "WebForm2.aspx?id=101&name=Raj"
);
```

### Receiving

```csharp
string id = Request.QueryString["id"];
string name = Request.QueryString["name"];
```

### Query String Syntax

```text
WebForm2.aspx?id=101&name=Raj&type=Permanent
             ↑       ↑
             ?       &
```

- `?` starts the query string.
- `&` separates multiple parameters.

### Advantages

- Very simple.
- No server-side storage required.
- Bookmarkable.
- Easy to debug.
- Works across requests.

### Disadvantages

- Data is visible in the URL.
- URL length is limited.
- Should not be used for passwords or sensitive data.
- Users can modify the values.

### Good Use

```text
Product.aspx?id=1001
Employee.aspx?id=501
Order.aspx?orderId=10025
```

### Bad Use

```text
Login.aspx?password=MyPassword
```

---

# 20. Cookies

Cookies store small pieces of data in the user's browser.

### Creating a Cookie

```csharp
HttpCookie cookie = new HttpCookie("Employee");

cookie["Name"] = "Raj";
cookie["Id"] = "101";

Response.Cookies.Add(cookie);
```

### Reading a Cookie

```csharp
HttpCookie cookie = Request.Cookies["Employee"];

if (cookie != null)
{
    string name = cookie["Name"];
    string id = cookie["Id"];
}
```

## Persistent Cookie

Set the `Expires` property:

```csharp
cookie.Expires = DateTime.Now.AddDays(7);
```

The browser can retain the cookie after closing.

## Non-Persistent Cookie

Don't set `Expires`.

```csharp
Response.Cookies["UserName"].Value = "Raj";
```

It normally lasts for the browser session.

### Advantages

- Stored on the client.
- Available across requests.
- Useful for preferences.
- Persistent cookies can survive browser restarts.

### Disadvantages

- Users can disable cookies.
- Users can modify cookie values.
- Storage is limited.
- Sensitive information should not be stored in plaintext.
- The client controls the storage.

---

# 21. Session State

Session stores data associated with a particular user's session.

```csharp
Session["EmployeeId"] = 101;
Session["EmployeeName"] = "Raj";
```

Another page:

```csharp
int id = (int)Session["EmployeeId"];

string name = Session["EmployeeName"].ToString();
```

Conceptually:

```text
             ASP.NET Server
                  |
        ┌─────────┴─────────┐
        |                   |
    Session A           Session B
        |                   |
     Raj's data          John's data
```

Session A normally cannot access Session B's values.

---

# 22. How Does ASP.NET Identify a Session?

This is a common interview question.

By default, ASP.NET uses a **session ID**.

Conceptually:

```text
Browser
   |
   | ASP.NET_SessionId
   ↓
Server
   |
   ↓
Find corresponding session data
```

The session ID is normally stored in a cookie.

Important distinction:

> **The session ID identifies the session; the Session State Mode determines where the session data is stored.**

For example:

```text
Session ID
    ↓
Identifies user session
    ↓
Session State Provider
    ↓
InProc / StateServer / SQLServer / Custom
    ↓
Actual session data
```

---

# 23. Session State Modes

ASP.NET Framework Web Forms supports:

```text
Off
InProc
StateServer
SQLServer
Custom
```

---

# 24. Session State Mode = Off

```xml
<sessionState mode="Off" />
```

Session state is disabled for the application.

### Advantages

- No session-state overhead.
- Less memory usage.
- Can improve performance when sessions aren't required.
- Useful for stateless pages.

### Disadvantages

- Session cannot be used.
- User-specific server-side session data is unavailable.

### Page Level

```aspx
<%@ Page EnableSessionState="false" %>
```

---

# 25. Session State Mode = InProc

`InProc` is the **default** session-state mode.

```xml
<sessionState
    mode="InProc"
    timeout="20" />
```

### Where Is Data Stored?

Session data is stored in the memory of the ASP.NET worker process.

```text
Browser
   |
   | Session ID
   ↓
Web Server
   |
   ↓
ASP.NET Worker Process
   |
   └── Session Data
```

### Why Is InProc Fast?

The session object is held in local server memory.

There is no need to make a network/database call for normal session retrieval.

### Advantages

1. **Very fast**
   - Memory access is fast.

2. **Easy to configure**
   ```xml
   <sessionState mode="InProc" />
   ```

3. **No external storage dependency**
   - No State Service or SQL Server is required.

4. **No serialization requirement for normal in-process storage**
   - Objects can be stored directly in process memory.

5. **Good for single-server applications**
   - Especially suitable for small/simple Web Forms applications.

### Disadvantages

1. **Session is lost when the worker process restarts**

```text
Application Pool Recycle
        ↓
Worker Process Restart
        ↓
Memory Cleared
        ↓
Session Lost
```

2. **Not naturally suitable for Web Farms**

```text
             Load Balancer
             /           \
        Server A       Server B
        Session A       Session B
```

If a user's request moves from Server A to Server B, Server B does not automatically have Server A's in-memory session.

3. **Not suitable for Web Gardens**

Multiple worker processes on the same server have separate memory spaces.

4. **Memory consumption**
   - Large numbers of sessions consume application memory.

5. **Scalability limitations**
   - Session data is tied to the worker process.

### Best Use

Use InProc when:

- The application is hosted on a single server.
- Performance is important.
- Session loss during application restart is acceptable.
- Session objects are relatively small.

---

# 26. Web Garden vs Web Farm

Understanding these is important for Session State.

## Web Garden

Multiple worker processes run on the **same server**.

```text
One Server
 ├── Worker Process 1
 ├── Worker Process 2
 └── Worker Process 3
```

Each worker process has separate memory.

Therefore:

```text
Process 1 → Session A
Process 2 → Session A  ❌
```

The session is not automatically shared between worker processes.

## Web Farm

Multiple servers host the same application.

```text
             Load Balancer
             /           \
        Server A       Server B
```

InProc session is tied to one server's worker-process memory.

For scalable multi-server applications, external session storage is generally preferred.

---

# 27. Session State Mode = StateServer

StateServer moves session data outside the ASP.NET worker process.

```xml
<sessionState
    mode="StateServer"
    stateConnectionString="tcpip=StateServer:42424"
    timeout="20" />
```

Architecture:

```text
Browser
   |
   | Session ID
   ↓
Web Server
   |
   | Connection
   ↓
ASP.NET State Service
   |
   └── Session Data
```

The ASP.NET State Service runs separately from the ASP.NET worker process.

### Why Use StateServer?

With InProc:

```text
Worker Process Restart
        ↓
Session Lost
```

With StateServer:

```text
Worker Process Restart
        ↓
Worker process memory is cleared
        ↓
State Service still contains session data
```

Therefore, session data is independent of the application worker process.

### Advantages

1. **Survives worker-process restart**
2. **External session storage**
3. **Suitable for Web Farms/Web Gardens when properly configured**
4. **Better scalability than InProc**
5. **Session data is not tied directly to one worker process**

### Disadvantages

1. **Slower than InProc**
   - Communication with the State Service adds overhead.

2. **Serialization required for objects**
   - Complex session objects generally need to be serializable.

3. **State Service dependency**
   - If the State Service is unavailable, session access is affected.

4. **Possible single point of failure**
   - If all applications depend on one StateServer and it fails, the sessions stored there can be lost.

### Best Use

Use StateServer when:

- You need session storage outside the worker process.
- Worker-process restarts should not immediately destroy session state.
- A distributed deployment needs shared session storage.
- SQL Server session storage is unnecessary or too heavy.

---

# 28. Session State Mode = SQLServer

SQLServer mode stores session data in SQL Server.

```xml
<sessionState
    mode="SQLServer"
    sqlConnectionString="data source=.;Integrated Security=SSPI"
    timeout="20" />
```

Architecture:

```text
Browser
   |
   | Session ID
   ↓
Web Server
   |
   ↓
SQL Server
   |
   └── Session Data
```

### Why Use SQLServer?

Consider a Web Farm:

```text
             Load Balancer
            /      |      \
           /       |       \
      Server A  Server B  Server C
           \       |       /
            \      |      /
             SQL Server
```

All application servers can use the same external session store.

### Advantages

1. **Survives worker-process restart**
2. **Suitable for Web Farms**
3. **Suitable for Web Gardens**
4. **Centralized session storage**
5. **More scalable than InProc**
6. **Session data is outside application-server memory**

### Disadvantages

1. **Slower than InProc**
   - Database access introduces latency.

2. **Serialization/deserialization**
   - Complex objects need to be serialized for external storage.

3. **Database dependency**
   - SQL Server must be available.

4. **Database overhead**
   - Large session usage can increase database load.

5. **More configuration**
   - ASP.NET session-state database must be configured before use.

### Best Use

Use SQLServer when:

- The application runs on multiple servers.
- Session state must be shared.
- Worker-process recycling should not lose sessions.
- A centralized database-backed session store is acceptable.

---

# 29. Session State Mode = Custom

`Custom` allows a custom session-state provider to determine how and where session data is stored.

```xml
<sessionState mode="Custom">
    ...
</sessionState>
```

Conceptually:

```text
ASP.NET
   |
   ↓
Custom Session Provider
   |
   ├── Custom Database
   ├── Distributed Cache
   ├── Redis-like store
   └── Other storage
```

The exact storage depends on the provider implementation.

### Advantages

1. Maximum flexibility.
2. Can integrate with specialized distributed storage.
3. Storage can be designed for the application's architecture.
4. Can be optimized for specific scalability requirements.

### Disadvantages

1. More complicated to implement/configure.
2. More maintenance.
3. Provider performance becomes your responsibility.
4. Debugging can be harder.
5. Incorrect provider implementation can cause reliability or concurrency problems.

### Best Use

Use Custom when the built-in session providers don't satisfy the application's requirements.

---

# 30. Complete Session Mode Comparison

| Feature | InProc | StateServer | SQLServer | Custom |
|---|---|---|---|---|
| Storage | Worker process memory | ASP.NET State Service | SQL Server | Custom provider |
| Speed | Fastest | Medium | Slower | Depends |
| Survives worker restart | No | Yes | Yes | Depends |
| Web Farm friendly | Generally no | Yes | Yes | Depends |
| Web Garden friendly | Generally no | Yes | Yes | Depends |
| Serialization | Generally not required | Required for relevant objects | Required for relevant objects | Depends |
| Configuration | Very easy | Moderate | More complex | Complex |
| External dependency | No | State Service | SQL Server | Provider dependent |
| Scalability | Limited | Better | High | Depends |
| Default | Yes | No | No | No |

---

# 31. InProc vs StateServer vs SQLServer

Remember this progression:

```text
                    SESSION DATA
                         |
        ┌────────────────┼────────────────┐
        ↓                ↓                ↓
      InProc         StateServer       SQLServer
        |                |                |
      Memory          Service          Database
        |                |                |
     Fastest          Medium           Slower
        |                |                |
   Simple/local     External          External
                    storage           storage
```

### InProc

> Fastest and simplest, but tied to worker-process memory.

### StateServer

> External session storage that is independent of the worker process, but introduces service and serialization overhead.

### SQLServer

> Database-backed session storage that is well suited to Web Farms, but introduces database and serialization overhead.

---

# 32. Session Timeout

Example:

```xml
<sessionState
    mode="InProc"
    timeout="20" />
```

The timeout represents approximately **20 minutes of inactivity**, not 20 minutes from login.

Example:

```text
10:00 → Request
10:10 → Request
10:25 → Request
```

Activity can refresh the session timeout.

If there is no activity for the configured timeout period, the session can expire.

---

# 33. Session vs Cookie

Don't confuse the two.

### Cookie

```text
Browser
   ↓
Stores data on client
```

### Session

```text
Browser
   ↓
Session ID
   ↓
Server
   ↓
Session data
```

Therefore:

> **Cookie is client-side storage; Session data is server-side storage.**

However, by default, the **session ID itself is commonly stored in a cookie**.

---

# 34. Session vs Application State

### Session

```text
User A → Session["Name"] = "Raj"
User B → Session["Name"] = "John"
```

Each user has separate session data.

### Application

```text
Application["CompanyName"] = "ABC"
```

All users can access the same application-level value.

```text
                Application
                /    |    \
             User A User B User C
                \    |    /
                 Same data
```

---

# 35. Application State Thread Safety

Application state is shared across requests.

This can create race conditions:

```csharp
Application["Count"] =
    (int)Application["Count"] + 1;
```

Use locking when modifying shared application state:

```csharp
Application.Lock();

Application["Count"] =
    (int)Application["Count"] + 1;

Application.UnLock();
```

Conceptually:

```text
Request A → Lock → Update → Unlock
Request B → Wait
Request B → Lock → Update → Unlock
```

---

# 36. Choosing the Right Data Transfer Technique

### Small, non-sensitive data

Use:

```text
Query String
```

Example:

```text
Employee.aspx?id=101
```

### Client preferences

Use:

```text
Cookie
```

Example:

```text
Theme = Dark
```

### User-specific server-side data

Use:

```text
Session
```

Example:

```csharp
Session["EmployeeId"] = 101;
```

### Data during Server.Transfer

Use:

```text
Context.Handler
```

### Form submission from one page to another

Use:

```text
Cross-Page PostBack
```

### Global application-wide data

Use:

```text
Application State
```

---

# 37. Final Mental Model

```text
                    DATA TRANSFER
                         |
       ┌─────────────────┼──────────────────┐
       ↓                 ↓                  ↓
   Client-side       Request/Page       Server-side
       |                 |                  |
   ┌───┴───┐         ┌───┴────┐        ┌────┴─────┐
   ↓       ↓         ↓        ↓        ↓          ↓
Cookie  QueryString Context   Cross   Session  Application
                   Handler   Page
                              Postback
```

Session:

```text
                    SESSION
                       |
        ┌──────────────┼───────────────┐
        ↓              ↓               ↓
      InProc       StateServer      SQLServer
        |              |               |
     Memory         Service         Database
        |              |               |
     Fastest        External        External
     simplest       storage         storage
        |              |               |
   Restart loses   Process-safe     Process-safe
   session        better           + Web Farm
```

---

# 38. Five Things to Remember for Interviews

1. **QueryString → URL/client-visible → small, non-sensitive data**
2. **Cookie → client-side → preferences/small client data**
3. **Session → one user's server-side data**
4. **Application → shared by all users**
5. **InProc → fastest/default but tied to worker-process memory; StateServer and SQLServer move session storage outside the worker process**

---

# Practical Exercises

- [ ] Create two Web Forms and transfer data using Cross-Page PostBack.
- [ ] Transfer data using `Context.Handler` and `Server.Transfer()`.
- [ ] Pass Employee ID and Employee Name using QueryString.
- [ ] Create and read a persistent Cookie.
- [ ] Create and read a non-persistent Cookie.
- [ ] Store Employee information in Session.
- [ ] Configure Session timeout in `web.config`.
- [ ] Disable Session State for a page.
- [ ] Test `InProc` session behavior.
- [ ] Understand `StateServer` configuration.
- [ ] Understand `SQLServer` Session State configuration.
- [ ] Compare InProc, StateServer, and SQLServer.
- [ ] Store a global counter using Application State.
- [ ] Use `Application.Lock()` and `Application.UnLock()`.
