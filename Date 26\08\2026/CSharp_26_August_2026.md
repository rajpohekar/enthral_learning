# C# Learning – 26 August 2026

## Delegates

### What is a Delegate?
A delegate is a type-safe reference to a method. It allows a method to be stored in a variable and invoked later.

### Declaration
```csharp
public delegate void MyDelegate();
```

### Invocation
```csharp
void ShowMessage()
{
    Console.WriteLine("Hello");
}

MyDelegate d = ShowMessage;
d();
```

### Action Delegate
`Action` is a built-in delegate that returns `void`.

```csharp
Action<string> print = message => Console.WriteLine(message);
print("Hello");
```

### Func Delegate
`Func` is a built-in delegate that returns a value. The last generic parameter represents the return type.

```csharp
Func<int, int, int> add = (a, b) => a + b;

int result = add(10, 20);
```

## Events

An event is a notification mechanism built using delegates. It allows an object to notify subscribers when something happens.

Typical flow:

```text
Something happens
      ↓
Event is raised
      ↓
Subscribed event handler executes
```

### Event Handler

```csharp
void Button_Click(object sender, EventArgs e)
{
    // Handle the event
}
```

### sender

`sender` is the object that raised the event.

### EventArgs

`EventArgs` contains information associated with the event. Custom `EventArgs` classes can be created when additional data needs to be passed.

### Event Subscription

Use `+=` to subscribe:

```csharp
button.Click += Button_Click;
```

Use `-=` to unsubscribe:

```csharp
button.Click -= Button_Click;
```

## Key Relationship

```text
Delegate
   ↓
Defines the method signature

Event
   ↓
Uses the delegate to notify subscribers

Event Handler
   ↓
Method that executes when the event is raised
```

## Key Takeaways

- Delegates can reference methods.
- `Action` represents methods returning `void`.
- `Func` represents methods that return a value.
- Events provide controlled notifications to subscribers.
- `sender` identifies the event source.
- `EventArgs` carries event information.
- `+=` subscribes to an event.
- `-=` unsubscribes from an event.
