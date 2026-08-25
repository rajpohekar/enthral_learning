# ASP.NET Web Forms — Validation Controls

## Date: 25/08/2026

Validation controls in ASP.NET Web Forms are used to validate user input and ensure that the data entered into form controls is valid.

ASP.NET provides **6 built-in validation controls**:

1. `RequiredFieldValidator`
2. `RangeValidator`
3. `RegularExpressionValidator`
4. `CompareValidator`
5. `CustomValidator`
6. `ValidationSummary`

Validation controls can perform both:

- **Client-side validation**
- **Server-side validation**

Client-side validation improves user experience by showing validation errors without immediately sending the request to the server. However, server-side validation should still be performed because client-side JavaScript can be disabled or bypassed.

---

# 1. RequiredFieldValidator

`RequiredFieldValidator` checks whether the user has entered a value in an input control.

### Example

```aspx
<asp:TextBox ID="txtName" runat="server"></asp:TextBox>

<asp:RequiredFieldValidator
    ID="rfvName"
    runat="server"
    ControlToValidate="txtName"
    ErrorMessage="Name is required"
    ForeColor="Red">
</asp:RequiredFieldValidator>
```

For a `DropDownList`, `InitialValue` can be used to specify the value that represents an unselected state.

### Important Properties

| Property | Purpose |
|---|---|
| `ControlToValidate` | Specifies the control to validate |
| `ErrorMessage` | Message displayed when validation fails |
| `Text` | Short validation message displayed beside the control |
| `InitialValue` | Value considered empty/unselected |
| `SetFocusOnError` | Moves focus to the invalid control |
| `Display` | Controls how the validation message is rendered |
| `Enabled` | Enables/disables the validator |
| `EnableClientScript` | Enables/disables client-side validation |

---

# 2. RangeValidator

`RangeValidator` checks whether a value falls within a specified range.

### Example

```aspx
<asp:TextBox ID="txtAge" runat="server"></asp:TextBox>

<asp:RangeValidator
    ID="rvAge"
    runat="server"
    ControlToValidate="txtAge"
    Type="Integer"
    MinimumValue="1"
    MaximumValue="100"
    ErrorMessage="Age must be between 1 and 100">
</asp:RangeValidator>
```

### Important Properties

| Property | Purpose |
|---|---|
| `ControlToValidate` | Input control being validated |
| `Type` | Data type of the value |
| `MinimumValue` | Minimum allowed value |
| `MaximumValue` | Maximum allowed value |

### Supported Types

Common types include:

```text
String
Integer
Double
Date
Currency
```

### Example

For an age:

```text
MinimumValue = 1
MaximumValue = 100
Type = Integer
```

For a date:

```text
MinimumValue = 01/01/2012
MaximumValue = 31/12/2012
Type = Date
```

---

# 3. Display Property

The `Display` property is supported by all validation controls.

### `Display="None"`

The error message is not rendered next to the control.

This is useful when the errors are displayed only through `ValidationSummary`.

### `Display="Static"`

Space is reserved on the page for the validation message even when validation succeeds.

The generated HTML generally uses `visibility:hidden` when there is no error.

### `Display="Dynamic"`

Space is not reserved when there is no error.

The generated HTML generally uses `display:none` until the validation message needs to be shown.

### Comparison

```text
None
    ↓
No message beside control

Static
    ↓
Space reserved for message

Dynamic
    ↓
Space appears only when needed
```

---

# 4. CompareValidator

`CompareValidator` compares the value of one control with another control or with a constant value.

It can be used for:

- Confirm password
- Comparing dates
- Comparing numbers
- Data type checking
- Equality/inequality checks

### Example — Confirm Password

```aspx
<asp:TextBox
    ID="txtPassword"
    runat="server"
    TextMode="Password">
</asp:TextBox>

<asp:TextBox
    ID="txtConfirmPassword"
    runat="server"
    TextMode="Password">
</asp:TextBox>

<asp:CompareValidator
    ID="cvPassword"
    runat="server"
    ControlToValidate="txtConfirmPassword"
    ControlToCompare="txtPassword"
    Operator="Equal"
    Type="String"
    ErrorMessage="Passwords must match">
</asp:CompareValidator>
```

### Comparison Operators

```text
Equal
GreaterThan
GreaterThanEqual
LessThan
LessThanEqual
NotEqual
DataTypeCheck
```

---

# 5. CompareValidator Properties

The important properties specific to `CompareValidator` are:

| Property | Purpose |
|---|---|
| `ControlToValidate` | Control whose value is being validated |
| `ControlToCompare` | Another control whose value is compared |
| `Type` | Data type used for comparison |
| `Operator` | Comparison operation |
| `ValueToCompare` | Constant value to compare against |

### Compare Two Controls

```aspx
ControlToCompare="txtPassword"
Operator="Equal"
```

### Compare With a Constant

```aspx
ValueToCompare="18"
Operator="GreaterThanEqual"
Type="Integer"
```

### Data Type Checking

`CompareValidator` can also validate whether the entered value can be interpreted as the specified data type.

Example:

```aspx
<asp:CompareValidator
    ID="cvAge"
    runat="server"
    ControlToValidate="txtAge"
    Operator="DataTypeCheck"
    Type="Integer"
    ErrorMessage="Age must be a number">
</asp:CompareValidator>
```

---

# 6. SetFocusOnError

`SetFocusOnError` is supported by validation controls.

When:

```aspx
SetFocusOnError="true"
```

and validation fails, the browser automatically moves focus to the associated input control.

Example:

```aspx
<asp:RequiredFieldValidator
    ID="rfvName"
    runat="server"
    ControlToValidate="txtName"
    ErrorMessage="Name is required"
    SetFocusOnError="true">
</asp:RequiredFieldValidator>
```

---

# 7. RegularExpressionValidator

`RegularExpressionValidator` validates whether an input matches a specified **regular expression pattern**.

It is useful for validating formats such as:

- Email
- Phone number
- PIN code
- Postal code
- Custom text patterns

### Example — Email

```aspx
<asp:TextBox
    ID="txtEmail"
    runat="server">
</asp:TextBox>

<asp:RegularExpressionValidator
    ID="revEmail"
    runat="server"
    ControlToValidate="txtEmail"
    ValidationExpression="^[^@\s]+@[^@\s]+\.[^@\s]+$"
    ErrorMessage="Invalid email">
</asp:RegularExpressionValidator>
```

The important property specific to this validator is:

```text
ValidationExpression
```

### Important Note

`RegularExpressionValidator` checks the **format** of the value.

It does not prove that the email address actually exists.

---

# 8. Client-Side Validation

Client-side validation is enabled by default for ASP.NET validation controls when supported by the browser.

To disable client-side validation:

```aspx
EnableClientScript="false"
```

Example:

```aspx
<asp:RequiredFieldValidator
    ID="rfvName"
    runat="server"
    ControlToValidate="txtName"
    ErrorMessage="Name is required"
    EnableClientScript="false">
</asp:RequiredFieldValidator>
```

### Why Server-Side Validation Is Still Required

Client-side validation can be bypassed or disabled.

Therefore, important validation should also be enforced on the server.

A typical flow is:

```text
User enters data
       ↓
Client-side validation
       ↓
If valid → request sent
       ↓
Server-side validation
       ↓
Process data
```

---

# 9. CustomValidator

`CustomValidator` is used when the built-in validation controls cannot satisfy a specific business rule.

For example:

```text
Only even numbers are allowed.
Employee ID must follow a custom company rule.
A value must satisfy a business-specific condition.
```

### Example

Suppose only positive even numbers are allowed.

```aspx
<asp:TextBox
    ID="txtNumber"
    runat="server">
</asp:TextBox>

<asp:CustomValidator
    ID="cvNumber"
    runat="server"
    ControlToValidate="txtNumber"
    OnServerValidate="CustomValidator_ServerValidate"
    ErrorMessage="Please enter a positive even number">
</asp:CustomValidator>
```

### Server-Side Validation

```csharp
protected void CustomValidator_ServerValidate(
    object source,
    ServerValidateEventArgs args)
{
    int number = Convert.ToInt32(args.Value);

    if (number > 0 && number % 2 == 0)
    {
        args.IsValid = true;
    }
    else
    {
        args.IsValid = false;
    }
}
```

The key property is:

```text
OnServerValidate
```

It specifies the server-side validation method.

---

# 10. Client-Side Custom Validation

`CustomValidator` can also support client-side validation.

Important property:

```text
ClientValidationFunction
```

This specifies the JavaScript function used for client-side validation.

Example:

```aspx
<asp:CustomValidator
    ID="cvNumber"
    runat="server"
    ControlToValidate="txtNumber"
    ClientValidationFunction="validateNumber"
    OnServerValidate="CustomValidator_ServerValidate"
    ErrorMessage="Invalid number">
</asp:CustomValidator>
```

### Important CustomValidator Properties

| Property | Purpose |
|---|---|
| `OnServerValidate` | Server-side validation method |
| `ClientValidationFunction` | Client-side JavaScript validation function |
| `ValidateEmptyText` | Determines whether validation occurs when the input is empty |

`ValidateEmptyText` is `false` by default.

---

# 11. ValidationSummary

`ValidationSummary` displays a summary of validation errors from the page in one location.

Instead of showing every detailed error in multiple places, the page can show a consolidated list.

### Example

```aspx
<asp:ValidationSummary
    ID="ValidationSummary1"
    runat="server"
    HeaderText="Page Errors"
    ShowSummary="true"
    ShowMessageBox="false"
    DisplayMode="BulletList" />
```

### Important Properties

| Property | Purpose |
|---|---|
| `HeaderText` | Heading displayed above the summary |
| `ShowSummary` | Whether errors are displayed in the summary |
| `ShowMessageBox` | Whether errors are displayed in a browser message box |
| `DisplayMode` | Format used to display errors |

### DisplayMode Values

```text
List
BulletList
SingleParagraph
```

### Common Real-World Pattern

A common UI pattern is:

```text
Email             *
Username          *
Password          *
Confirm Password  *

Page Errors
Email is required
Username is required
Password is required
Password and Confirm Password must match
```

The individual validation controls can display short indicators beside fields, while `ValidationSummary` displays detailed messages.

---

# 12. Validation Groups

By default, clicking a button can cause all validation controls on the page to participate in validation.

This becomes a problem when a page contains multiple forms or independent sections.

Example:

```text
-------------------------
Login
-------------------------
Username
Password
Login

-------------------------
Registration
-------------------------
Email
Username
Password
Confirm Password
Register
Clear
```

Clicking `Login` should validate only the Login fields.

Clicking `Register` should validate only the Registration fields.

This is achieved using **ValidationGroup**.

---

## ValidationGroup Example

### Login Validators

```aspx
<asp:RequiredFieldValidator
    ID="rfvLoginUser"
    runat="server"
    ControlToValidate="txtLoginUser"
    ValidationGroup="LoginGroup"
    ErrorMessage="Username is required">
</asp:RequiredFieldValidator>

<asp:RequiredFieldValidator
    ID="rfvLoginPassword"
    runat="server"
    ControlToValidate="txtLoginPassword"
    ValidationGroup="LoginGroup"
    ErrorMessage="Password is required">
</asp:RequiredFieldValidator>
```

### Login Button

```aspx
<asp:Button
    ID="btnLogin"
    runat="server"
    Text="Login"
    ValidationGroup="LoginGroup" />
```

### Registration Validators

```aspx
<asp:RequiredFieldValidator
    ID="rfvEmail"
    runat="server"
    ControlToValidate="txtEmail"
    ValidationGroup="RegisterGroup"
    ErrorMessage="Email is required">
</asp:RequiredFieldValidator>
```

### Registration Button

```aspx
<asp:Button
    ID="btnRegister"
    runat="server"
    Text="Register"
    ValidationGroup="RegisterGroup" />
```

Now:

```text
Login button
    ↓
LoginGroup validators only

Register button
    ↓
RegisterGroup validators only
```

---

# 13. CausesValidation

Sometimes a button should perform an action without triggering validation.

For example, a **Clear** button should simply clear fields. It should not show validation errors.

Use:

```aspx
CausesValidation="false"
```

Example:

```aspx
<asp:Button
    ID="btnClear"
    runat="server"
    Text="Clear"
    CausesValidation="false" />
```

### Important Difference

```text
CausesValidation
    ↓
Controls whether a button triggers validation.

ValidationGroup
    ↓
Controls which group of validators participates.
```

---

# 14. Important Validation Properties to Remember

### Common to many/all validation controls

```text
ControlToValidate
ErrorMessage
Text
Display
SetFocusOnError
Enabled
EnableClientScript
ValidationGroup
```

### Validator-specific properties

```text
RangeValidator
    Type
    MinimumValue
    MaximumValue

CompareValidator
    ControlToCompare
    Operator
    Type
    ValueToCompare

RegularExpressionValidator
    ValidationExpression

CustomValidator
    OnServerValidate
    ClientValidationFunction
    ValidateEmptyText

ValidationSummary
    HeaderText
    ShowSummary
    ShowMessageBox
    DisplayMode
```

---

# 15. Validation Flow in ASP.NET Web Forms

A typical Web Forms form follows this pattern:

```text
                    User submits form
                           ↓
                 Client-side validation
                           ↓
                    Validation passes?
                     ↙             ↘
                   No               Yes
                   ↓                 ↓
             Show errors       Send request
                                     ↓
                              Server-side validation
                                     ↓
                              Page.IsValid
                                ↙       ↘
                              No         Yes
                              ↓           ↓
                         Show errors   Process data
```

On the server side, the most important property to check is:

```csharp
if (Page.IsValid)
{
    // Process data
}
```

Example:

```csharp
protected void btnSave_Click(object sender, EventArgs e)
{
    if (Page.IsValid)
    {
        // Save data to database
    }
}
```

---

# 16. Quick Comparison

| Validator | Main Purpose | Key Property |
|---|---|---|
| `RequiredFieldValidator` | Required input | `ControlToValidate` |
| `RangeValidator` | Value within range | `MinimumValue`, `MaximumValue` |
| `CompareValidator` | Compare values / type checking | `ControlToCompare`, `Operator` |
| `RegularExpressionValidator` | Pattern/format validation | `ValidationExpression` |
| `CustomValidator` | Custom business logic | `OnServerValidate` |
| `ValidationSummary` | Display validation errors together | `DisplayMode` |

---

# 17. Interview Questions

### Q1. What are validation controls?

ASP.NET Web Forms controls that validate user input and provide client-side and server-side validation support.

### Q2. What are the six built-in validation controls?

```text
RequiredFieldValidator
RangeValidator
RegularExpressionValidator
CompareValidator
CustomValidator
ValidationSummary
```

### Q3. Why do we need server-side validation if client-side validation exists?

Client-side validation can be disabled or bypassed, so server-side validation is necessary to enforce validation reliably.

### Q4. What is the difference between RequiredFieldValidator and RangeValidator?

`RequiredFieldValidator` checks whether a value has been entered, while `RangeValidator` checks whether the value falls within a specified range.

### Q5. How do you validate a confirm-password field?

Use `CompareValidator` with:

```aspx
ControlToCompare="txtPassword"
Operator="Equal"
```

### Q6. How do you validate an email format?

Use `RegularExpressionValidator` with an appropriate `ValidationExpression`.

### Q7. When would you use CustomValidator?

When the validation requirement is a custom business rule that is not adequately handled by the built-in validators.

### Q8. What is ValidationGroup?

It groups validation controls and associates them with a specific button/control so that only the relevant validators execute.

### Q9. How do you prevent a Clear button from triggering validation?

```aspx
CausesValidation="false"
```

### Q10. How do you check whether all server-side validations passed?

```csharp
if (Page.IsValid)
{
    // Valid
}
```

---

# 18. Practical Example — Employee Form

For an Employee Master page, validation could look like:

```text
Employee Name
    → RequiredFieldValidator

Employee Type
    → RequiredFieldValidator / appropriate selection validation

Date of Joining
    → CompareValidator / custom date validation

Email
    → RegularExpressionValidator

Employee ID
    → RangeValidator / CompareValidator

Custom business rule
    → CustomValidator

All validation errors
    → ValidationSummary
```

Example server-side save:

```csharp
protected void btnSave_Click(object sender, EventArgs e)
{
    if (!Page.IsValid)
    {
        return;
    }

    // Validate/process data
    // Call stored procedure
    // Save employee record
}
```

---

# Key Takeaways

1. ASP.NET Web Forms provides **6 built-in validation controls**.
2. Validation can happen on both the **client and server**.
3. Never rely only on client-side validation for important business rules.
4. `RequiredFieldValidator` checks required input.
5. `RangeValidator` checks a value within a range.
6. `CompareValidator` compares values or performs data type checking.
7. `RegularExpressionValidator` validates a pattern.
8. `CustomValidator` implements custom validation logic.
9. `ValidationSummary` displays validation errors together.
10. `ValidationGroup` separates validation for different sections/buttons.
11. `CausesValidation="false"` prevents a button from triggering validation.
12. `Page.IsValid` is important when processing server-side form submissions.
13. `SetFocusOnError="true"` moves focus to the invalid control.
14. `EnableClientScript="false"` disables client-side validation for a validator.
