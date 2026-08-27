# C# Learning – 27 August 2026

## Events and Delegates – Deeper Understanding

Today's focus was on understanding how delegates and events work together, especially while creating custom events.

## 1. Delegate

A delegate is a type-safe reference to a method.

A delegate defines the signature that a method must follow.

```csharp
public delegate void CalendarVisibilityChangedEventHandler(
    object sender,
    CalendarVisibilityChangedEventArgs e
);
```

The event handler method must match this signature.

```csharp
void CalendarVisibilityChanged(
    object sender,
    CalendarVisibilityChangedEventArgs e)
{
}
```

## 2. Event

An event provides a notification mechanism.

```csharp
public event CalendarVisibilityChangedEventHandler
    CalendarVisibilityChanged;
```

The event can have one or more subscribers.

## 3. EventArgs

`EventArgs` is used to carry information about an event.

For a custom event, a custom `EventArgs` class can be created:

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

Here, `IsVisible` tells the subscriber whether the Calendar is currently visible.

## 4. Raising an Event

A common pattern is to create a protected virtual `On<EventName>()` method.

```csharp
protected virtual void OnCalendarVisibilityChanged(
    CalendarVisibilityChangedEventArgs e)
{
    CalendarVisibilityChanged?.Invoke(this, e);
}
```

The important part is:

```csharp
CalendarVisibilityChanged?.Invoke(this, e);
```

This raises the event only when there is at least one subscriber.

## 5. Custom Event Flow

```text
Something happens inside User Control
            ↓
OnCalendarVisibilityChanged()
            ↓
CalendarVisibilityChanged event is raised
            ↓
Subscribed event handler is invoked
            ↓
Parent Web Form responds
```

## 6. Publisher and Subscriber

The User Control acts as the publisher.

The parent Web Form acts as the subscriber.

```text
CalendarUserControl
       │
       │ raises event
       ↓
CalendarVisibilityChanged
       │
       ↓
Default.aspx event handler
```

This allows the User Control to communicate without directly depending on a specific parent page.

## Key Takeaways

- A delegate defines the signature of an event handler.
- An event provides the notification mechanism.
- `EventArgs` carries event data.
- `Invoke()` raises the event.
- `+=` subscribes a handler.
- `-=` removes a handler.
- `sender` identifies the object raising the event.
- A custom event is useful when a reusable component needs to notify its consumer about something that happened internally.
