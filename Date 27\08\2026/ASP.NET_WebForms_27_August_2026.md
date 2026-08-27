# ASP.NET Web Forms Learning – 27 August 2026

## 1. User Controls in ASP.NET

A Web User Control is a reusable component that combines one or more server controls or HTML controls into a single reusable control.

Example:

```text
CalendarUserControl
├── TextBox
├── ImageButton
└── Calendar
```

Instead of repeating this markup and code on multiple Web Forms, the functionality can be encapsulated inside one User Control.

### Why use User Controls?

- Code reusability
- Encapsulation
- Reduced duplication
- Easier maintenance
- Easier testing
- Faster development

Example use case:

If multiple Web Forms need a Date of Birth field with a TextBox, calendar button, and Calendar control, the entire functionality can be placed in one User Control and reused.

---

## 2. Basic Calendar User Control Example

The User Control can contain:

- TextBox for displaying the selected date
- ImageButton to show/hide the Calendar
- Calendar control for selecting a date
- Button for processing the selected date

Typical flow:

```text
User clicks calendar button
        ↓
Calendar becomes visible
        ↓
User selects a date
        ↓
Selected date is copied to TextBox
        ↓
Calendar becomes hidden
```

Example event handling inside the User Control:

```csharp
protected void ImgBtn_Click(object sender, ImageClickEventArgs e)
{
    if (Calendar1.Visible)
    {
        Calendar1.Visible = false;
    }
    else
    {
        Calendar1.Visible = true;
    }
}

protected void Calendar1_SelectionChanged(object sender, EventArgs e)
{
    txtDate.Text = Calendar1.SelectedDate.ToShortDateString();
    Calendar1.Visible = false;
}

protected void Button1_Click(object sender, EventArgs e)
{
    Response.Write(txtDate.Text);
}
```

The important point is that these events initially work **inside the User Control**.

---

# 3. Custom Events in a User Control

Sometimes the parent Web Form needs to know when something happens inside the User Control.

For example:

> The parent page should know whenever the Calendar visibility changes.

A custom event can be created for this.

## Five Steps

### Step 1 – Create EventArgs

Create a custom `EventArgs` class to carry event-specific data.

```csharp
public class CalendarVisibilityChangedEventArgs : EventArgs
{
    public bool IsVisible { get; set; }

    public CalendarVisibilityChangedEventArgs(bool isVisible)
    {
        IsVisible = isVisible;
    }
}
```

### Step 2 – Create the Delegate

The delegate defines the signature of the event handler.

```csharp
public delegate void CalendarVisibilityChangedEventHandler(
    object sender,
    CalendarVisibilityChangedEventArgs e
);
```

### Step 3 – Create the Event

```csharp
public event CalendarVisibilityChangedEventHandler
    CalendarVisibilityChanged;
```

### Step 4 – Create the Event-Raising Method

```csharp
protected virtual void OnCalendarVisibilityChanged(
    CalendarVisibilityChangedEventArgs e)
{
    CalendarVisibilityChanged?.Invoke(this, e);
}
```

### Step 5 – Raise the Event

Whenever the Calendar visibility changes:

```csharp
Calendar1.Visible = false;

OnCalendarVisibilityChanged(
    new CalendarVisibilityChangedEventArgs(false)
);
```

---

# 4. Consuming a Custom Event

The parent Web Form needs an event-handler method.

The method signature must match the custom delegate.

```csharp
protected void CalendarUserControl1_CalendarVisibilityChanged(
    object sender,
    CalendarVisibilityChangedEventArgs e)
{
    if (e.IsVisible)
    {
        // Calendar is visible
    }
    else
    {
        // Calendar is hidden
    }
}
```

The handler must be registered before the event is raised.

Using code:

```csharp
CalendarUserControl1.CalendarVisibilityChanged
    += CalendarUserControl1_CalendarVisibilityChanged;
```

Using markup is also possible when the User Control exposes the event.

---

# 5. Event Subscription

Use:

```csharp
+=
```

to subscribe.

Use:

```csharp
-=
```

to unsubscribe.

Conceptually:

```text
User Control
     │
     │ raises event
     ↓
CalendarVisibilityChanged
     │
     │ subscribed handler
     ↓
Parent Web Form
```

If there are no subscribers, the event can be `null`.

Therefore, use:

```csharp
CalendarVisibilityChanged?.Invoke(this, e);
```

The `?.` prevents an exception when there are no subscribers.

---

# 6. Custom DateSelected Event

A second custom event can notify the parent Web Form when a date is selected.

The pattern is the same:

```text
Date selected
     ↓
Create DateSelectedEventArgs
     ↓
Create DateSelectedEventHandler delegate
     ↓
Create DateSelected event
     ↓
Create OnDateSelection()
     ↓
Raise the event
     ↓
Parent Web Form handles it
```

This demonstrates that custom events can represent meaningful business/component-level actions, not only built-in ASP.NET control events.

---

# 7. Loading User Controls Dynamically

Normally, User Controls can be added at design time by dragging them onto the Web Form.

However, they can also be loaded dynamically at runtime.

## Why load dynamically?

Different User Controls may be required depending on runtime conditions.

Examples:

```text
Logged-in user
     ↓
User preferences
     ↓
Load appropriate User Control
```

or:

```text
Admin user
     ↓
Admin User Control

Non-Admin user
     ↓
Different User Control
```

This is useful when the required control is not known until runtime.

---

# 8. Placeholder / Panel

A `PlaceHolder` or `Panel` can be used as the location where a dynamically created User Control should be added.

Conceptually:

```text
Web Form
│
├── Header
│
├── PlaceHolder
│      ↓
│   Dynamic User Control
│
└── Footer
```

---

# 9. Dynamic User Control and PostBack

A dynamically loaded control may need to be recreated on every request if its state and events need to participate correctly in the Web Forms lifecycle.

The material discussed loading dynamic controls during the page lifecycle, including `Page_Init()` and `Page_Load()`.

Important principle:

> Dynamic controls should be recreated consistently on postback so that ASP.NET can restore their state and wire their events correctly.

`Page_Init()` is commonly recommended because it occurs early in the lifecycle, although the exact behavior can depend on how the control is created and used.

---

# 10. Key Concepts to Remember

### User Control

Reusable collection of controls and code.

### Custom Event

Allows a User Control to notify its consumer about an action or state change.

### EventArgs

Carries event-specific information.

### Delegate

Defines the event-handler method signature.

### Event Handler

Method that responds when the event is raised.

### `+=`

Subscribe to an event.

### `-=`

Unsubscribe from an event.

### `Invoke()`

Raises/calls the subscribed event handlers.

### Dynamic User Control

A User Control created/loaded at runtime instead of being declared only at design time.

---

# Overall Flow

```text
             USER CONTROL
                  │
        ┌─────────┴─────────┐
        │                   │
   Calendar             ImageButton
        │                   │
        └─────────┬─────────┘
                  │
             Something
              happens
                  ↓
          Custom Event Raised
                  ↓
          Parent Web Form
                  ↓
            Event Handler
                  ↓
             Response
```
