# ASP.NET WebForms Prep

## Topics Covered — 24/08/2026

Today I covered the following ASP.NET Web Forms server controls and concepts:

1. TextBox Control
2. TextBox Properties, Methods and Events
3. RadioButton Control
4. CheckBox Control
5. HyperLink Control
6. Button, LinkButton and ImageButton
7. Click Event and Command Event
8. CommandName and CommandArgument
9. DropDownList Control
10. DropDownList Items and ListItem
11. DropDownList Data Binding
12. DataTextField and DataValueField
13. Reading XML data using DataSet.ReadXml()
14. Server.MapPath()
15. ListItem.Insert()

---

# 1. TextBox Control

`TextBox` is an ASP.NET Web Forms server control used to accept text input from the user.

### Basic Syntax

```aspx
<asp:TextBox
    ID="txtName"
    runat="server">
</asp:TextBox>
```

### Example

```aspx
<asp:TextBox
    ID="txtName"
    runat="server"
    MaxLength="50">
</asp:TextBox>

<asp:Button
    ID="btnSubmit"
    runat="server"
    Text="Submit"
    OnClick="btnSubmit_Click" />
```

```csharp
protected void btnSubmit_Click(object sender, EventArgs e)
{
    string name = txtName.Text;

    Response.Write("Hello " + name);
}
```

The important point is:

```csharp
txtName.Text
```

is used to read the value entered by the user.

---

# 2. TextBox TextMode

The `TextMode` property controls the type of input that the TextBox accepts.

Common values:

- `SingleLine`
- `MultiLine`
- `Password`

### SingleLine

```aspx
<asp:TextBox
    ID="txtName"
    runat="server"
    TextMode="SingleLine">
</asp:TextBox>
```

Used for normal single-line input.

### MultiLine

```aspx
<asp:TextBox
    ID="txtAddress"
    runat="server"
    TextMode="MultiLine"
    Rows="5"
    Columns="40">
</asp:TextBox>
```

Used when the user needs to enter multiple lines of text.

`Rows` controls the approximate number of visible lines.

`Columns` controls the approximate width in characters.

### Password

```aspx
<asp:TextBox
    ID="txtPassword"
    runat="server"
    TextMode="Password">
</asp:TextBox>
```

The entered characters are displayed as masked characters.

---

# 3. Important TextBox Properties

## Text

Gets or sets the text contained in the TextBox.

```csharp
string value = txtName.Text;
```

Set the value:

```csharp
txtName.Text = "Raj";
```

---

## MaxLength

Specifies the maximum number of characters the user can enter.

```aspx
<asp:TextBox
    ID="txtUsername"
    runat="server"
    MaxLength="20">
</asp:TextBox>
```

The user cannot normally enter more than 20 characters through the browser control.

---

## ReadOnly

Prevents the user from editing the TextBox.

```aspx
<asp:TextBox
    ID="txtEmployeeId"
    runat="server"
    ReadOnly="true">
</asp:TextBox>
```

The server can still set/read its value.

---

## ToolTip

Displays a tooltip when the mouse is placed over the control.

```aspx
<asp:TextBox
    ID="txtEmail"
    runat="server"
    ToolTip="Enter your email address">
</asp:TextBox>
```

---

## Rows and Columns

Primarily useful with a multiline TextBox.

```aspx
<asp:TextBox
    ID="txtDescription"
    runat="server"
    TextMode="MultiLine"
    Rows="5"
    Columns="40">
</asp:TextBox>
```

---

## Height and Width

Control the rendered dimensions of the TextBox.

```aspx
<asp:TextBox
    ID="txtName"
    runat="server"
    Width="250px"
    Height="30px">
</asp:TextBox>
```

---

## AutoPostBack

When set to `true`, the control automatically causes a postback when its value changes and the change is detected by the server.

```aspx
<asp:TextBox
    ID="txtSearch"
    runat="server"
    AutoPostBack="true"
    OnTextChanged="txtSearch_TextChanged">
</asp:TextBox>
```

Example:

```csharp
protected void txtSearch_TextChanged(object sender, EventArgs e)
{
    string searchText = txtSearch.Text;

    // Perform search/filter operation
}
```

### Important

`AutoPostBack` is useful when you want the server to react immediately to a control change instead of waiting for another button click.

---

# 4. TextBox Event — TextChanged

`TextChanged` occurs when the text in the TextBox has changed and the change is processed by the server.

Example:

```aspx
<asp:TextBox
    ID="txtName"
    runat="server"
    AutoPostBack="true"
    OnTextChanged="txtName_TextChanged">
</asp:TextBox>

<asp:Label
    ID="lblMessage"
    runat="server">
</asp:Label>
```

```csharp
protected void txtName_TextChanged(object sender, EventArgs e)
{
    lblMessage.Text = "Name changed to: " + txtName.Text;
}
```

### Relationship

```text
User changes TextBox
        ↓
AutoPostBack = true
        ↓
Postback
        ↓
TextChanged event
        ↓
Server-side code executes
```

---

# 5. TextBox Focus()

`Focus()` sets the input focus to the TextBox.

```csharp
txtName.Focus();
```

Example:

```csharp
protected void Page_Load(object sender, EventArgs e)
{
    if (!IsPostBack)
    {
        txtName.Focus();
    }
}
```

This is commonly used to automatically place the cursor in an input field when the page loads.

---

# 6. RadioButton Control

`RadioButton` is used when the user should select one option from a group of mutually exclusive options.

### Basic Syntax

```aspx
<asp:RadioButton
    ID="rbMale"
    runat="server"
    Text="Male"
    GroupName="Gender" />

<asp:RadioButton
    ID="rbFemale"
    runat="server"
    Text="Female"
    GroupName="Gender" />
```

Because both controls have:

```text
GroupName = "Gender"
```

only one can be selected at a time.

---

# 7. RadioButton Properties

## Checked

Indicates whether the RadioButton is selected.

```csharp
if (rbMale.Checked)
{
    // Male selected
}
```

Set it:

```csharp
rbMale.Checked = true;
```

---

## Text

Text displayed next to the RadioButton.

```aspx
<asp:RadioButton
    ID="rbPermanent"
    runat="server"
    Text="Permanent"
    GroupName="EmployeeType" />
```

---

## TextAlign

Controls whether the text appears on the left or right side of the RadioButton.

```aspx
<asp:RadioButton
    ID="rbOption"
    runat="server"
    Text="Option A"
    TextAlign="Right" />
```

---

## AutoPostBack

Automatically causes a postback when the checked state changes.

```aspx
<asp:RadioButton
    ID="rbPermanent"
    runat="server"
    Text="Permanent"
    GroupName="EmployeeType"
    AutoPostBack="true"
    OnCheckedChanged="rbPermanent_CheckedChanged" />
```

---

## GroupName

Defines the group to which the RadioButton belongs.

```aspx
<asp:RadioButton
    ID="rbMale"
    runat="server"
    Text="Male"
    GroupName="Gender" />

<asp:RadioButton
    ID="rbFemale"
    runat="server"
    Text="Female"
    GroupName="Gender" />
```

### Key Concept

Same `GroupName`:

```text
RadioButton A ─┐
               ├── Only one can be selected
RadioButton B ─┘
```

Different `GroupName` values allow independent selections.

---

# 8. RadioButton Event — CheckedChanged

Occurs when the checked state of a RadioButton changes.

```aspx
<asp:RadioButton
    ID="rbPermanent"
    runat="server"
    Text="Permanent"
    GroupName="EmployeeType"
    AutoPostBack="true"
    OnCheckedChanged="rbPermanent_CheckedChanged" />
```

```csharp
protected void rbPermanent_CheckedChanged(object sender, EventArgs e)
{
    if (rbPermanent.Checked)
    {
        lblMessage.Text = "Permanent employee selected.";
    }
}
```

---

# 9. CheckBox Control

`CheckBox` allows the user to independently select or deselect an option.

Example:

```aspx
<asp:CheckBox
    ID="chkTerms"
    runat="server"
    Text="I accept the terms and conditions" />
```

Unlike RadioButtons, multiple CheckBoxes can be selected independently.

---

# 10. CheckBox Properties

## Checked

Checks whether the CheckBox is selected.

```csharp
if (chkTerms.Checked)
{
    // Terms accepted
}
```

Set it:

```csharp
chkTerms.Checked = true;
```

---

## Text

Text displayed next to the CheckBox.

```aspx
<asp:CheckBox
    ID="chkActive"
    runat="server"
    Text="Active Employee" />
```

---

## TextAlign

Controls the position of the text relative to the CheckBox.

```aspx
<asp:CheckBox
    ID="chkOption"
    runat="server"
    Text="Option"
    TextAlign="Right" />
```

---

## AutoPostBack

Causes a postback when the checked state changes.

```aspx
<asp:CheckBox
    ID="chkActive"
    runat="server"
    Text="Active"
    AutoPostBack="true"
    OnCheckedChanged="chkActive_CheckedChanged" />
```

---

# 11. CheckBox Focus()

Like TextBox, CheckBox also supports `Focus()`.

```csharp
chkTerms.Focus();
```

---

# 12. CheckBox Event — CheckedChanged

Triggered when the checked state changes.

```csharp
protected void chkActive_CheckedChanged(object sender, EventArgs e)
{
    if (chkActive.Checked)
    {
        lblStatus.Text = "Employee is active.";
    }
    else
    {
        lblStatus.Text = "Employee is inactive.";
    }
}
```

---

# 13. HyperLink Control

`HyperLink` is used to create a link that navigates to another page or URL.

### Basic Syntax

```aspx
<asp:HyperLink
    ID="hlGoogle"
    runat="server"
    Text="Google"
    NavigateUrl="https://www.google.com">
</asp:HyperLink>
```

Unlike a Button, a HyperLink is primarily for navigation.

---

# 14. HyperLink Properties

## Text

Text displayed to the user.

```aspx
Text="Open Dashboard"
```

---

## NavigateUrl

Specifies the destination URL.

```aspx
NavigateUrl="Dashboard.aspx"
```

Example:

```aspx
<asp:HyperLink
    ID="hlDashboard"
    runat="server"
    Text="Dashboard"
    NavigateUrl="Dashboard.aspx">
</asp:HyperLink>
```

---

## ImageUrl

Displays an image instead of normal link text.

```aspx
<asp:HyperLink
    ID="hlHome"
    runat="server"
    ImageUrl="~/Images/home.png"
    NavigateUrl="~/Default.aspx">
</asp:HyperLink>
```

If an image is specified, the image is normally displayed as the link content.

---

## Target

Specifies where the linked page should open.

```aspx
Target="_blank"
```

Example:

```aspx
<asp:HyperLink
    ID="hlGoogle"
    runat="server"
    Text="Google"
    NavigateUrl="https://www.google.com"
    Target="_blank">
</asp:HyperLink>
```

`_blank` opens the destination in a new browsing context.

---

# 15. HyperLink Focus()

```csharp
hlDashboard.Focus();
```

This sets focus to the HyperLink control.

### HyperLink Events

HyperLink does not expose a server-side `Click` event in the same way that Button and LinkButton do. Its primary purpose is navigation.

---

# 16. Button Control

A `Button` is used to submit/post the page back to the server and execute server-side logic.

```aspx
<asp:Button
    ID="btnSave"
    runat="server"
    Text="Save"
    OnClick="btnSave_Click" />
```

Code-behind:

```csharp
protected void btnSave_Click(object sender, EventArgs e)
{
    lblMessage.Text = "Record saved.";
}
```

---

# 17. LinkButton Control

`LinkButton` behaves like a server-side Button but is rendered with a link-like appearance.

```aspx
<asp:LinkButton
    ID="lnkSave"
    runat="server"
    Text="Save"
    OnClick="lnkSave_Click">
</asp:LinkButton>
```

```csharp
protected void lnkSave_Click(object sender, EventArgs e)
{
    lblMessage.Text = "Saved using LinkButton.";
}
```

---

# 18. ImageButton Control

`ImageButton` allows an image to act as a server-side button.

```aspx
<asp:ImageButton
    ID="imgSave"
    runat="server"
    ImageUrl="~/Images/save.png"
    OnClick="imgSave_Click" />
```

```csharp
protected void imgSave_Click(object sender, ImageClickEventArgs e)
{
    lblMessage.Text = "Image button clicked.";
}
```

---

# 19. Button vs LinkButton vs ImageButton

| Control | Appearance | Main Use |
|---|---|---|
| Button | Normal push button | Submit/action |
| LinkButton | Link-like | Server action with link appearance |
| ImageButton | Image | Image-based action |

All three can perform server-side actions.

---

# 20. Click Event

A Button exposes a `Click` event.

```aspx
<asp:Button
    ID="btnDelete"
    runat="server"
    Text="Delete"
    OnClick="btnDelete_Click" />
```

```csharp
protected void btnDelete_Click(object sender, EventArgs e)
{
    // Delete logic
}
```

The `Click` event is useful when a particular button has its own dedicated behavior.

---

# 21. Command Event

ASP.NET Web Forms buttons also expose a `Command` event.

The important properties are:

- `CommandName`
- `CommandArgument`

Example:

```aspx
<asp:Button
    ID="btnEdit"
    runat="server"
    Text="Edit"
    CommandName="EditEmployee"
    CommandArgument="101"
    OnCommand="Button_Command" />
```

Code-behind:

```csharp
protected void Button_Command(
    object sender,
    CommandEventArgs e)
{
    string command = e.CommandName.ToString();
    string employeeId = e.CommandArgument.ToString();

    if (command == "EditEmployee")
    {
        lblMessage.Text =
            "Edit employee: " + employeeId;
    }
}
```

---

# 22. Why Command Event is Useful

Suppose a GridView contains many Edit/Delete buttons.

Instead of creating separate event handlers for every button, a common command handler can determine:

```text
Which command?
        +
Which record?
        ↓
Perform required action
```

For example:

```text
CommandName     = "Edit"
CommandArgument = "101"
```

means:

```text
Edit employee whose ID = 101
```

Another row could contain:

```text
CommandName     = "Delete"
CommandArgument = "102"
```

The same handler can process both commands.

---

# 23. Click vs Command

### Click

Used when the button has a specific action.

```csharp
protected void btnSave_Click(object sender, EventArgs e)
{
    SaveEmployee();
}
```

### Command

Useful when multiple controls perform related actions and the handler needs command information.

```csharp
protected void Button_Command(
    object sender,
    CommandEventArgs e)
{
    if (e.CommandName == "Edit")
    {
        // Edit
    }
    else if (e.CommandName == "Delete")
    {
        // Delete
    }
}
```

### Important Event Order

When a Button raises both events:

```text
Click event
    ↓
Command event
```

The `Click` event occurs before the `Command` event.

---

# 24. DropDownList Control

`DropDownList` displays a list of options where the user can select an item.

Example:

```aspx
<asp:DropDownList
    ID="ddlCountry"
    runat="server">
    <asp:ListItem Text="Select" Value="0" />
    <asp:ListItem Text="India" Value="101" />
    <asp:ListItem Text="USA" Value="102" />
    <asp:ListItem Text="Australia" Value="103" />
    <asp:ListItem Text="UK" Value="104" />
</asp:DropDownList>
```

The dropdown contains `ListItem` objects.

---

# 25. ListItem

Each option in a DropDownList is represented by a `ListItem`.

A ListItem commonly contains:

```text
Text
Value
Selected
Enabled
```

Example:

```aspx
<asp:ListItem
    Text="India"
    Value="101" />
```

Here:

```text
Text  = India
Value = 101
```

The user sees:

```text
India
```

but the application can use:

```text
101
```

as the underlying value.

---

# 26. Adding ListItems at Runtime

Items can also be added using C#.

```csharp
ddlCountry.Items.Add(
    new ListItem("India", "101"));

ddlCountry.Items.Add(
    new ListItem("USA", "102"));
```

---

# 27. Selected Property

To select an item:

```csharp
ddlCountry.Items[1].Selected = true;
```

Or:

```aspx
<asp:ListItem
    Text="India"
    Value="101"
    Selected="true" />
```

---

# 28. Enabled Property

An individual ListItem can be disabled.

```aspx
<asp:ListItem
    Text="Not Available"
    Value="0"
    Enabled="false" />
```

This prevents the user from selecting that option normally.

---

# 29. Avoiding Duplicate Items in Page_Load

If you add DropDownList items in `Page_Load`, remember that `Page_Load` executes on every request.

Bad:

```csharp
protected void Page_Load(object sender, EventArgs e)
{
    ddlCountry.Items.Add(
        new ListItem("India", "101"));
}
```

After postbacks, items can be added repeatedly.

Better:

```csharp
protected void Page_Load(object sender, EventArgs e)
{
    if (!IsPostBack)
    {
        ddlCountry.Items.Add(
            new ListItem("India", "101"));

        ddlCountry.Items.Add(
            new ListItem("USA", "102"));
    }
}
```

### Why?

```text
Initial Page Load
        ↓
!IsPostBack = true
        ↓
Add items

Button Click / Postback
        ↓
!IsPostBack = false
        ↓
Do not add items again
```

This is an important Web Forms concept.

---

# 30. Reading the Selected DropDownList Item

### SelectedValue

Gets the value of the selected item.

```csharp
string countryId = ddlCountry.SelectedValue;
```

If India is selected:

```text
SelectedValue = "101"
```

### SelectedItem

Gets the selected `ListItem`.

```csharp
string countryName =
    ddlCountry.SelectedItem.Text;
```

For India:

```text
SelectedItem.Text = "India"
```

---

# 31. DropDownList Data Binding

Instead of manually adding every ListItem, a DropDownList can be populated from a data source.

Common data sources include:

- Database
- DataTable
- DataSet
- XML
- Collections
- Other objects

Example concept:

```text
Data Source
     ↓
Data Binding
     ↓
DropDownList
     ↓
ListItems
```

---

# 32. DataTextField and DataValueField

When binding structured data, two important properties are:

### DataTextField

Specifies the field that should be displayed to the user.

### DataValueField

Specifies the field that should be used as the underlying value.

Example:

```aspx
<asp:DropDownList
    ID="ddlCountry"
    runat="server"
    DataTextField="CountryName"
    DataValueField="CountryId">
</asp:DropDownList>
```

Suppose the data is:

| CountryId | CountryName |
|---:|---|
| 101 | India |
| 102 | USA |
| 103 | Australia |
| 104 | UK |

The user sees:

```text
India
USA
Australia
UK
```

But the selected values are:

```text
101
102
103
104
```

---

# 33. Important Rule Before DataBind()

Set `DataTextField` and `DataValueField` before calling `DataBind()`.

Example:

```csharp
ddlCountry.DataTextField = "CountryName";
ddlCountry.DataValueField = "CountryId";

ddlCountry.DataSource = dtCountries;
ddlCountry.DataBind();
```

Think of it as:

```text
DataTextField
      ↓
What should the user SEE?

DataValueField
      ↓
What VALUE should the application use?

DataBind()
      ↓
Create the DropDownList items
```

---

# 34. Complete Data Binding Example

Suppose we have a DataTable:

```csharp
DataTable dt = new DataTable();

dt.Columns.Add("CountryId");
dt.Columns.Add("CountryName");

dt.Rows.Add("101", "India");
dt.Rows.Add("102", "USA");
dt.Rows.Add("103", "Australia");
dt.Rows.Add("104", "UK");
```

Bind it:

```csharp
ddlCountry.DataSource = dt;

ddlCountry.DataTextField = "CountryName";
ddlCountry.DataValueField = "CountryId";

ddlCountry.DataBind();
```

Result:

```text
Dropdown displayed to user:

Select
India
USA
Australia
UK
```

And internally:

```text
India     → 101
USA       → 102
Australia → 103
UK        → 104
```

---

# 35. XML Data Binding

ASP.NET Web Forms can also populate a DropDownList using XML data.

Example XML:

```xml
<Countries>
    <Country>
        <CountryId>101</CountryId>
        <CountryName>India</CountryName>
    </Country>

    <Country>
        <CountryId>102</CountryId>
        <CountryName>USA</CountryName>
    </Country>

    <Country>
        <CountryId>103</CountryId>
        <CountryName>Australia</CountryName>
    </Country>

    <Country>
        <CountryId>104</CountryId>
        <CountryName>UK</CountryName>
    </Country>
</Countries>
```

The XML contains structured country data.

---

# 36. DataSet.ReadXml()

`DataSet.ReadXml()` can read XML data into a DataSet.

Example:

```csharp
DataSet ds = new DataSet();

string path = Server.MapPath("~/Data/Countries.xml");

ds.ReadXml(path);
```

After reading the XML, the DataSet can be used as the source for data binding.

Example:

```csharp
DataSet ds = new DataSet();

string path = Server.MapPath("~/Data/Countries.xml");

ds.ReadXml(path);

ddlCountry.DataSource = ds.Tables[0];
ddlCountry.DataTextField = "CountryName";
ddlCountry.DataValueField = "CountryId";
ddlCountry.DataBind();
```

---

# 37. Server.MapPath()

`Server.MapPath()` converts a virtual web path into a physical path on the server.

Example:

```csharp
string path =
    Server.MapPath("~/Data/Countries.xml");
```

Conceptually:

```text
Virtual path
~/Data/Countries.xml
        ↓
Server.MapPath()
        ↓
Physical path on server
C:\...\YourApplication\Data\Countries.xml
```

This is useful when server-side code needs to access a file.

---

# 38. Complete XML → DataSet → DropDownList Example

### Countries.xml

```xml
<Countries>
    <Country>
        <CountryId>101</CountryId>
        <CountryName>India</CountryName>
    </Country>

    <Country>
        <CountryId>102</CountryId>
        <CountryName>USA</CountryName>
    </Country>

    <Country>
        <CountryId>103</CountryId>
        <CountryName>Australia</CountryName>
    </Country>

    <Country>
        <CountryId>104</CountryId>
        <CountryName>UK</CountryName>
    </Country>
</Countries>
```

### ASPX

```aspx
<asp:DropDownList
    ID="ddlCountry"
    runat="server">
</asp:DropDownList>
```

### Code-behind

```csharp
protected void Page_Load(object sender, EventArgs e)
{
    if (!IsPostBack)
    {
        DataSet ds = new DataSet();

        string path =
            Server.MapPath("~/Data/Countries.xml");

        ds.ReadXml(path);

        ddlCountry.DataSource = ds.Tables[0];

        ddlCountry.DataTextField = "CountryName";
        ddlCountry.DataValueField = "CountryId";

        ddlCountry.DataBind();

        ddlCountry.Items.Insert(
            0,
            new ListItem("Select", "0"));
    }
}
```

Result:

```text
Select
India
USA
Australia
UK
```

---

# 39. ListItem.Insert()

`Insert()` adds a ListItem at a specific index.

Syntax:

```csharp
ddlCountry.Items.Insert(
    index,
    new ListItem("Text", "Value"));
```

Example:

```csharp
ddlCountry.Items.Insert(
    0,
    new ListItem("Select", "0"));
```

Because index `0` is the first position, `Select` appears at the top.

---

# 40. Why Insert() is Useful

Suppose data binding creates:

```text
India
USA
Australia
UK
```

We want:

```text
Select
India
USA
Australia
UK
```

Use:

```csharp
ddlCountry.Items.Insert(
    0,
    new ListItem("Select", "0"));
```

---

# 41. End-to-End Flow

A common Web Forms data-binding flow is:

```text
XML File
   ↓
Server.MapPath()
   ↓
Physical file path
   ↓
DataSet.ReadXml()
   ↓
DataTable
   ↓
DropDownList.DataSource
   ↓
DataTextField / DataValueField
   ↓
DataBind()
   ↓
ListItems created
   ↓
Items.Insert()
   ↓
Final DropDownList
```

---

# 42. Important Concepts to Remember

## TextBox

```text
Text        → Read/write entered text
TextMode    → SingleLine / MultiLine / Password
MaxLength   → Maximum characters
ReadOnly    → Prevent user editing
Rows        → Multiline visible rows
Columns     → Multiline width
AutoPostBack→ Postback on change
TextChanged → Event when text changes
Focus()     → Sets input focus
```

## RadioButton

```text
Checked         → Selected or not
Text            → Display text
TextAlign       → Text position
GroupName       → Defines mutually exclusive group
AutoPostBack    → Postback on state change
CheckedChanged  → State change event
```

## CheckBox

```text
Checked         → Selected or not
Text            → Display text
TextAlign       → Text position
AutoPostBack    → Postback on state change
CheckedChanged  → State change event
Focus()         → Sets focus
```

## HyperLink

```text
Text        → Link text
NavigateUrl → Destination
ImageUrl    → Image used for link
Target      → Where destination opens
Focus()     → Sets focus
```

## Button / LinkButton / ImageButton

```text
Button      → Normal button
LinkButton  → Link-style button
ImageButton → Image-based button

Click       → Direct button action
Command     → Command-based handling
CommandName → Type of operation
CommandArgument → Data identifying the target
```

## DropDownList

```text
ListItem        → Individual option
Selected        → Select an item
SelectedItem    → Selected ListItem
SelectedValue   → Selected value
DataSource      → Source of data
DataTextField   → Field displayed to user
DataValueField  → Underlying value
DataBind()      → Performs binding
```

---

# 43. Interview Questions

### Q1. What is the difference between RadioButton and CheckBox?

**RadioButton:**

Used when the user normally selects one option from a group.

**CheckBox:**

Used when options can be independently selected.

---

### Q2. Why do we use GroupName in RadioButton?

`GroupName` groups RadioButtons so that only one RadioButton in that group can be selected.

```aspx
GroupName="Gender"
```

---

### Q3. What is AutoPostBack?

`AutoPostBack` causes a Web Forms control to submit the page back to the server automatically when its relevant value/state changes.

---

### Q4. What is the difference between DataTextField and DataValueField?

```text
DataTextField  → What the user sees
DataValueField → What the application uses as the value
```

Example:

```text
CountryId = 101
CountryName = India

DataTextField  = CountryName
DataValueField = CountryId
```

The user sees:

```text
India
```

The selected value is:

```text
101
```

---

### Q5. Why do we use `if (!IsPostBack)` while binding controls in Page_Load?

Because `Page_Load` executes during both the initial request and postbacks.

Without the check, items/data may be rebound unnecessarily or manually added items may be duplicated.

---

### Q6. What does Server.MapPath() do?

It converts a virtual application path into a physical path on the server.

```csharp
Server.MapPath("~/Data/Countries.xml");
```

---

### Q7. What is DataSet.ReadXml()?

It reads XML data into a `DataSet`.

```csharp
DataSet ds = new DataSet();
ds.ReadXml(path);
```

---

### Q8. Why is CommandName useful?

It identifies the operation that a button represents.

Examples:

```text
Edit
Delete
View
Download
```

---

### Q9. Why is CommandArgument useful?

It carries additional information associated with the command.

For example:

```text
CommandName     = Delete
CommandArgument = 101
```

means:

```text
Delete employee 101
```

---

### Q10. What is the difference between Click and Command?

`Click` is useful for handling a button's direct click action.

`Command` is useful when the same handler needs to process different operations using `CommandName` and `CommandArgument`.

---

# 44. Practical Mini Example

A simple Employee form can combine many of today's controls:

```aspx
<asp:TextBox
    ID="txtName"
    runat="server"
    MaxLength="50">
</asp:TextBox>

<br />

<asp:RadioButton
    ID="rbPermanent"
    runat="server"
    Text="Permanent"
    GroupName="EmployeeType" />

<asp:RadioButton
    ID="rbContract"
    runat="server"
    Text="Contract"
    GroupName="EmployeeType" />

<br />

<asp:CheckBox
    ID="chkActive"
    runat="server"
    Text="Active" />

<br />

<asp:DropDownList
    ID="ddlCountry"
    runat="server">
</asp:DropDownList>

<br />

<asp:Button
    ID="btnSave"
    runat="server"
    Text="Save"
    OnClick="btnSave_Click" />
```

Code-behind:

```csharp
protected void btnSave_Click(object sender, EventArgs e)
{
    string name = txtName.Text;

    string employeeType;

    if (rbPermanent.Checked)
    {
        employeeType = "Permanent";
    }
    else
    {
        employeeType = "Contract";
    }

    bool isActive = chkActive.Checked;

    string countryId = ddlCountry.SelectedValue;

    // Save employee to database
}
```

This example combines:

```text
TextBox
RadioButton
CheckBox
DropDownList
Button
Server-side event handling
SelectedValue
```

These are core building blocks for an ASP.NET Web Forms CRUD application.

---

# 45. Today's Key Mental Model

The most important thing to understand is that ASP.NET Web Forms controls are **server controls**.

Example:

```aspx
<asp:TextBox
    ID="txtName"
    runat="server" />
```

The `runat="server"` attribute makes the control available to server-side C# code.

Therefore:

```text
ASPX page
   ↓
Server Control
   ↓
Postback
   ↓
C# Code-Behind
   ↓
Event Handler
   ↓
Business Logic / Database
   ↓
Response rendered back to browser
```

For today's controls, remember the following:

```text
TextBox      → User text input
RadioButton  → One option from a group
CheckBox     → Independent yes/no selection
HyperLink    → Navigation
Button       → Server-side action
LinkButton   → Link-style server action
ImageButton  → Image-based server action
DropDownList → Select one item
```

The next important step is to connect these controls with:

```text
Validation Controls
        ↓
Page Navigation
        ↓
QueryString / Cookies
        ↓
Session / Application State
        ↓
Database
        ↓
GridView + Data Binding
        ↓
CRUD Operations
```
