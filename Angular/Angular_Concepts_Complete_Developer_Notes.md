# Angular Concepts --- Complete Developer Notes

## Scope

These notes cover Angular from a **conceptual, developer-oriented
perspective**. The goal is to understand how Angular applications are
structured and how Angular works with an ASP.NET Core Web API.

This is intentionally **concept-first** rather than a coding tutorial.

------------------------------------------------------------------------

# 1. What is Angular?

Angular is a frontend web framework developed by Google for building web
applications.

Angular runs primarily in the browser and is used to build the user
interface and client-side application logic.

A typical full-stack application is:

``` text
Browser
   |
   v
Angular Frontend
   |
   | HTTP / JSON
   v
ASP.NET Core Web API
   |
   v
EF Core
   |
   v
SQL Server
```

Angular is not a database framework and is not a backend framework.

## Angular's responsibilities

Angular commonly handles:

-   User interface
-   Component structure
-   Client-side routing
-   Forms
-   Client-side validation
-   Calling APIs
-   Managing frontend state
-   Authentication-related client behavior
-   Display formatting
-   Reusable UI logic

ASP.NET Core handles:

-   API endpoints
-   Business rules
-   Authentication
-   Authorization
-   Database access
-   Server-side validation
-   Persistence

------------------------------------------------------------------------

# 2. Angular vs AngularJS

These are different generations of Google's framework.

  AngularJS                      Angular
  ------------------------------ -------------------------------------
  Original framework             Modern Angular
  JavaScript-focused             TypeScript-focused
  Older architecture             Modern component-based architecture
  Commonly associated with 1.x   Angular 2+ and later
  Controllers were prominent     Components are central

When people currently say **Angular**, they normally mean modern
Angular.

------------------------------------------------------------------------

# 3. Angular vs ASP.NET Core MVC

You already know ASP.NET Core MVC.

MVC:

``` text
Browser
   |
   v
ASP.NET Core MVC Controller
   |
   v
Model / Service
   |
   v
Database
   |
   v
Razor View
   |
   v
HTML returned to browser
```

Angular:

``` text
Browser
   |
   v
Angular
   |
   | HTTP
   v
ASP.NET Core Web API
   |
   v
Database
```

The key distinction is where the UI logic lives.

## MVC

The server generates HTML using Razor.

## Angular

Angular manages the UI in the browser and usually communicates with the
backend through APIs.

A useful conceptual comparison:

``` text
ASP.NET MVC              Angular

Razor View        <-->   Angular Template
Controller logic  <-->   Component logic
Service           <-->   Angular Service
Routing           <-->   Angular Router
Model/ViewModel   <-->   Model/Interface
```

These are conceptual parallels, not exact equivalents.

------------------------------------------------------------------------

# 4. SPA --- Single Page Application

Angular is commonly used to build Single Page Applications.

In a traditional multi-page application:

``` text
Request /employees
       |
       v
Server
       |
       v
New HTML page
```

Navigation can require a new document from the server.

In an SPA:

``` text
Angular application loaded
          |
          +--> /employees
          +--> /dashboard
          +--> /settings
```

Angular changes the displayed UI without necessarily loading a
completely new document for every route.

The browser keeps the application running while Angular changes
components/views.

------------------------------------------------------------------------

# 5. Angular Application Architecture

A conceptual Angular application looks like:

``` text
Angular Application
|
+-- Components
|
+-- Templates
|
+-- Services
|
+-- Dependency Injection
|
+-- Routing
|
+-- Forms
|
+-- Directives
|
+-- Pipes
|
+-- HTTP Client
|
+-- RxJS / Observables
|
+-- Guards
|
+-- Interceptors
|
+-- State Management
```

These concepts work together rather than independently.

------------------------------------------------------------------------

# 6. TypeScript

Angular applications are primarily written in TypeScript.

TypeScript is a superset of JavaScript that adds static typing and other
language features.

For a C# developer, TypeScript will feel familiar because it supports:

-   Classes
-   Interfaces
-   Access modifiers
-   Generics
-   Types
-   Functions
-   Modules
-   Object-oriented programming

Example concept:

``` text
C#
class Employee
{
    int Id;
    string Name;
}
```

Conceptually similar TypeScript:

``` text
class Employee
{
    id: number;
    name: string;
}
```

Important TypeScript concepts for Angular:

-   `string`
-   `number`
-   `boolean`
-   Arrays
-   Objects
-   Interfaces
-   Classes
-   Functions
-   Generics
-   Optional properties
-   Union types
-   Modules/imports
-   `async` / `await`
-   Promises

------------------------------------------------------------------------

# 7. Angular CLI

Angular CLI is the command-line tooling used to create, develop, build,
test, and maintain Angular applications.

Conceptually:

``` text
Angular CLI
    |
    +-- Create application
    +-- Generate components
    +-- Generate services
    +-- Build
    +-- Test
    +-- Development server
```

You do not need to memorize every CLI command.

Understand that CLI automates repetitive project setup and development
tasks.

------------------------------------------------------------------------

# 8. Angular Project Structure

A modern Angular project commonly contains concepts such as:

``` text
Angular Project
|
+-- src/
|   |
|   +-- app/
|   |   +-- components
|   |   +-- services
|   |   +-- routing
|   |   +-- application configuration
|   |
|   +-- assets/
|   +-- index.html
|   +-- main.ts
|
+-- package.json
+-- angular.json
+-- tsconfig.json
```

Exact structure varies with Angular version and whether the application
uses standalone APIs.

## package.json

Defines the project's npm dependencies and scripts.

Think of it as the frontend equivalent of a dependency manifest.

## angular.json

Contains Angular CLI workspace/build configuration.

## tsconfig.json

Contains TypeScript compiler configuration.

## main.ts

The application entry point used to bootstrap the Angular application.

## index.html

The main HTML document into which the Angular application is
bootstrapped.

------------------------------------------------------------------------

# 9. Components

A component is the fundamental building block of an Angular UI.

Conceptually:

``` text
Component
|
+-- TypeScript class
+-- Template
+-- Styling
+-- Metadata/configuration
```

Example application:

``` text
AppComponent
|
+-- NavbarComponent
+-- DashboardComponent
+-- EmployeeListComponent
+-- EmployeeDetailsComponent
+-- FooterComponent
```

A component generally manages a specific portion of the UI.

------------------------------------------------------------------------

# 10. Component Class

The component class contains UI-related state and behavior.

Conceptually:

``` text
EmployeeListComponent
|
+-- employees
+-- loading state
+-- error state
+-- selected employee
+-- UI methods
```

The class does not directly represent the database.

It represents the state and behavior needed by the UI.

------------------------------------------------------------------------

# 11. Component Template

A component template is the HTML representation of the component.

Angular templates extend normal HTML with Angular features such as:

-   Interpolation
-   Property binding
-   Event binding
-   Two-way binding
-   Directives
-   Pipes
-   Template variables
-   Control flow

Relationship:

``` text
Component Class
      |
      | state/data
      v
Template
      |
      v
Browser UI
```

------------------------------------------------------------------------

# 12. Component Hierarchy

Angular applications are built as trees of components.

``` text
AppComponent
|
+-- Header
|
+-- Sidebar
|
+-- MainContent
|   |
|   +-- Dashboard
|   +-- EmployeeList
|       |
|       +-- EmployeeCard
|
+-- Footer
```

This allows large applications to be broken into manageable pieces.

------------------------------------------------------------------------

# 13. Parent and Child Components

Components can contain other components.

For example:

``` text
EmployeePage
   |
   +-- EmployeeList
       |
       +-- EmployeeCard
```

Here:

-   `EmployeePage` is a parent.
-   `EmployeeList` is a child of `EmployeePage`.
-   `EmployeeCard` is a child of `EmployeeList`.

Communication between components is an important Angular concept.

------------------------------------------------------------------------

# 14. @Input --- Parent to Child

Input is used conceptually for passing data from a parent component to a
child component.

``` text
Parent
   |
   | data
   v
Child
```

Example:

``` text
Parent:
selectedEmployee

        |
        v

Child:
employee
```

Remember:

> Input means information coming into the child component.

------------------------------------------------------------------------

# 15. @Output --- Child to Parent

Output/event communication allows a child to notify its parent.

``` text
Child
   |
   | event
   v
Parent
```

Example:

``` text
EmployeeCard
      |
      | "delete requested"
      v
EmployeeList
```

Remember:

> Output is commonly used for child-to-parent event communication.

------------------------------------------------------------------------

# 16. Data Binding

Data binding is the mechanism connecting component data and the
template.

The four major concepts are:

``` text
Interpolation
Property Binding
Event Binding
Two-Way Binding
```

------------------------------------------------------------------------

# 17. Interpolation

Interpolation displays component data in a template.

Conceptually:

``` text
Component property
       |
       v
{{ value }}
       |
       v
HTML output
```

Example:

``` text
<h1>{{ employeeName }}</h1>
```

If the property is:

``` text
employeeName = "Raj"
```

the UI displays:

``` text
Raj
```

Interpolation is primarily used for displaying values.

------------------------------------------------------------------------

# 18. Property Binding

Property binding connects a component expression to a property of an
HTML element or Angular component.

Conceptually:

``` text
Component
   |
   v
HTML property
```

Example:

``` text
[disabled]="isSaving"
```

If `isSaving` is true, the corresponding element property becomes
disabled.

------------------------------------------------------------------------

# 19. Event Binding

Event binding handles events from the UI.

Conceptually:

``` text
User action
    |
    v
Browser event
    |
    v
Angular component method
```

Typical events:

-   Click
-   Input
-   Change
-   Submit
-   Key events

Example concept:

``` text
(click)="save()"
```

------------------------------------------------------------------------

# 20. Two-Way Binding

Two-way binding means data can flow in both directions.

``` text
Component
   <---->
 Template
```

User changes UI:

``` text
UI -> Component
```

Component changes value:

``` text
Component -> UI
```

This is particularly associated with forms and `ngModel`.

Modern Angular also supports more explicit reactive patterns, and
signal-based APIs can provide another way to model reactive state.

------------------------------------------------------------------------

# 21. Directives

A directive adds behavior to elements or changes how Angular renders
them.

Two broad categories:

``` text
Directives
|
+-- Structural
|
+-- Attribute
```

------------------------------------------------------------------------

# 22. Structural Directives / Control Flow

Structural directives influence what elements exist in the DOM.

Traditional Angular syntax includes:

``` text
*ngIf
*ngFor
*ngSwitch
```

Modern Angular also provides built-in control-flow syntax such as:

``` text
@if
@for
@switch
```

Conceptually:

``` text
@if condition
    |
    +-- render UI

@for items
    |
    +-- repeat UI
```

------------------------------------------------------------------------

# 23. Attribute Directives

Attribute directives modify the behavior or appearance of an existing
element.

Examples include:

``` text
ngClass
ngStyle
```

Conceptually:

``` text
HTML element
     |
     v
Directive
     |
     +-- changes appearance
     +-- changes behavior
```

Custom directives can also be created for reusable behavior.

------------------------------------------------------------------------

# 24. Pipes

Pipes transform values for display in templates.

Common built-in pipes include:

``` text
Date
Currency
Decimal
Percent
Uppercase
Lowercase
Json
```

Conceptually:

``` text
Raw value
    |
    v
Pipe
    |
    v
Display value
```

Example:

``` text
Date object
    |
    v
date pipe
    |
    v
18/09/2026
```

Pipes are primarily presentation transformations, not business logic.

------------------------------------------------------------------------

# 25. Services

A service is a reusable class that contains logic or functionality that
should not be tied to one particular UI component.

Common uses:

-   API communication
-   Authentication
-   Shared application logic
-   Logging
-   State sharing
-   Data transformation
-   Configuration-related functionality

Typical architecture:

``` text
Component
    |
    v
Service
    |
    v
HttpClient
    |
    v
ASP.NET Core API
```

------------------------------------------------------------------------

# 26. Why Services Exist

Without services, a component could become responsible for everything:

``` text
Component
|
+-- UI
+-- HTTP
+-- Authentication
+-- Data processing
+-- Business logic
+-- State
+-- Error handling
```

That becomes difficult to maintain.

Instead:

``` text
Component
    |
    +-- UI responsibility
    |
    v
Service
    |
    +-- reusable logic
    +-- API communication
```

This is separation of concerns.

------------------------------------------------------------------------

# 27. Dependency Injection

Angular has a dependency injection system.

You already know the concept from ASP.NET Core.

ASP.NET Core:

``` text
Controller
    |
    v
IEmployeeService
    |
    v
DI Container
```

Angular:

``` text
Component
    |
    v
EmployeeService
    |
    v
Angular Injector
```

The component declares what it needs instead of manually constructing
every dependency.

------------------------------------------------------------------------

# 28. Angular Injector

The injector is responsible for resolving dependencies.

Conceptually:

``` text
Component requests EmployeeService
              |
              v
          Injector
              |
              v
Creates/resolves service
              |
              v
        Component receives it
```

Providers tell Angular how a dependency should be created or supplied.

------------------------------------------------------------------------

# 29. Service Lifetime and Provider Scope

Angular dependency injection can be hierarchical.

A service can be provided at different levels, such as:

-   Application/root level
-   Route level
-   Component level

A root-provided service is commonly shared across the application.

Component-level providers can create a separate service instance for
that component subtree.

The exact lifecycle therefore depends on where the provider is
registered.

------------------------------------------------------------------------

# 30. Angular Routing

Angular Router maps URLs to components.

Conceptually:

``` text
URL
 |
 +-- /login       -> LoginComponent
 +-- /dashboard   -> DashboardComponent
 +-- /employees   -> EmployeeListComponent
 +-- /employees/1 -> EmployeeDetailsComponent
```

Routing allows an SPA to provide multiple application screens.

------------------------------------------------------------------------

# 31. Router Outlet

The router needs a location where the currently selected routed
component is displayed.

Conceptually:

``` text
Application Shell
|
+-- Navbar
|
+-- Router Outlet
|      |
|      +-- Current routed component
|
+-- Footer
```

When the route changes, Angular changes the component displayed in that
outlet.

------------------------------------------------------------------------

# 32. Route Parameters

Route parameters are values embedded in a URL.

Conceptually:

``` text
/employees/42
```

Here:

``` text
42 = employee ID
```

Angular can read that parameter and use it to load the appropriate data.

------------------------------------------------------------------------

# 33. Query Parameters

Query parameters appear after `?`.

Conceptually:

``` text
/employees?page=2&department=IT
```

They are useful for:

-   Filtering
-   Sorting
-   Pagination
-   Search
-   Optional UI state

------------------------------------------------------------------------

# 34. Child Routes

Routes can have nested routes.

Conceptually:

``` text
/admin
   |
   +-- /users
   +-- /employees
   +-- /reports
```

This allows complex applications to organize routes hierarchically.

------------------------------------------------------------------------

# 35. Lazy Loading

Large Angular applications do not necessarily need to load every feature
immediately.

Lazy loading means loading a feature when it is needed.

Conceptually:

``` text
Initial application
       |
       +-- core UI
       |
       +-- login

User opens Reports
       |
       v
Load Reports feature
```

Benefits:

-   Smaller initial bundle
-   Faster initial application load
-   Better scalability for large applications

------------------------------------------------------------------------

# 36. Route Guards

Route guards control whether navigation to a route should be allowed.

Typical example:

``` text
User tries /admin
       |
       v
Authentication/authorization check
       |
   +---+---+
   |       |
Allowed  Blocked
   |       |
   v       v
Admin    Login/Access page
```

Guards are client-side controls.

They should not replace backend authorization.

The ASP.NET Core API must independently enforce authorization.

------------------------------------------------------------------------

# 37. Angular Forms

Angular provides two major approaches:

``` text
Template-driven forms
Reactive forms
```

------------------------------------------------------------------------

# 38. Template-Driven Forms

Template-driven forms place much of the form configuration in the HTML
template.

They are generally convenient for simpler forms.

Conceptually:

``` text
HTML Template
      |
      v
Angular Forms
      |
      v
Form State
```

------------------------------------------------------------------------

# 39. Reactive Forms

Reactive forms define form structure and validation more explicitly in
TypeScript.

Important concepts:

``` text
FormControl
FormGroup
FormArray
FormBuilder
Validators
```

Conceptually:

``` text
FormGroup
|
+-- FormControl: Name
+-- FormControl: Email
+-- FormControl: Salary
```

Reactive forms are especially useful for larger or dynamically
structured forms.

------------------------------------------------------------------------

# 40. FormControl

A FormControl represents the state of an individual form field.

Conceptually:

``` text
Name field
    |
    v
FormControl
    |
    +-- value
    +-- valid/invalid
    +-- touched/untouched
    +-- dirty/pristine
```

------------------------------------------------------------------------

# 41. FormGroup

A FormGroup groups controls into a logical form.

``` text
Employee Form
|
+-- Name
+-- Email
+-- Department
+-- Salary
```

Each field is represented by a control.

------------------------------------------------------------------------

# 42. FormArray

FormArray is useful when the number of controls is dynamic.

For example:

``` text
Employee
|
+-- Skills
    |
    +-- Angular
    +-- C#
    +-- SQL
```

The user can add or remove skill fields.

------------------------------------------------------------------------

# 43. Validation

Angular forms support validation such as:

-   Required
-   Minimum/maximum length
-   Minimum/maximum value
-   Pattern
-   Email
-   Custom validation

Conceptually:

``` text
User input
    |
    v
Validation
    |
 +--+--+
 |     |
Valid Invalid
 |     |
 v     v
Submit Error message
```

Remember that client-side validation improves user experience but does
not replace server-side validation.

------------------------------------------------------------------------

# 44. HttpClient

HttpClient is Angular's primary mechanism for making HTTP requests.

It connects Angular to backend APIs.

Architecture:

``` text
Component
    |
    v
Service
    |
    v
HttpClient
    |
    v
HTTP
    |
    v
ASP.NET Core Web API
```

------------------------------------------------------------------------

# 45. HTTP Operations

Angular can make common HTTP requests:

``` text
GET     -> Retrieve
POST    -> Create
PUT     -> Replace/update
PATCH   -> Partial update
DELETE  -> Delete
```

Example conceptual API:

``` text
GET    /api/employees
GET    /api/employees/10
POST   /api/employees
PUT    /api/employees/10
DELETE /api/employees/10
```

------------------------------------------------------------------------

# 46. Angular API Service

A common architecture is:

``` text
EmployeeListComponent
        |
        v
EmployeeService
        |
        v
HttpClient
        |
        v
ASP.NET Core
```

The component should generally not know the low-level API implementation
details.

The service acts as an abstraction around API communication.

------------------------------------------------------------------------

# 47. JSON

The Angular frontend and ASP.NET Core API commonly communicate using
JSON.

Example:

``` json
{
  "id": 10,
  "name": "Raj",
  "department": "IT"
}
```

Request:

``` text
Angular
   |
   | JSON
   v
ASP.NET Core
```

Response:

``` text
ASP.NET Core
   |
   | JSON
   v
Angular
```

------------------------------------------------------------------------

# 48. DTOs Between Angular and ASP.NET Core

You already learned DTOs in Web API.

The same concept matters in a full-stack application.

``` text
Angular
   |
   | EmployeeDto
   v
ASP.NET Core API
```

A DTO defines the shape of data exchanged over the API.

For example:

``` text
EmployeeResponseDto
|
+-- id
+-- name
+-- department
```

The Angular side can have a corresponding TypeScript interface/model
representing the response shape.

------------------------------------------------------------------------

# 49. CORS

Angular and ASP.NET Core can run on different origins during
development.

For example:

``` text
Angular:
http://localhost:4200

API:
https://localhost:7000
```

The browser sees these as different origins.

CORS --- Cross-Origin Resource Sharing --- controls whether the browser
allows frontend JavaScript from one origin to access resources on
another origin.

Typical flow:

``` text
Angular
   |
   | request
   v
Browser CORS rules
   |
   v
ASP.NET Core API
```

CORS is configured on the backend.

------------------------------------------------------------------------

# 50. RxJS

Angular makes extensive use of RxJS.

RxJS is a library for reactive programming using **Observables**.

This is one of the most important Angular concepts for a .NET developer
to understand.

------------------------------------------------------------------------

# 51. Observable

An Observable represents a source of values that can be observed over
time.

Conceptually:

``` text
Observable
    |
    +-- value 1
    +-- value 2
    +-- value 3
    +-- ...
```

The source may emit:

-   HTTP responses
-   User events
-   Timers
-   WebSocket messages
-   Application state changes

------------------------------------------------------------------------

# 52. Observable vs Promise

A Promise generally represents one eventual result.

``` text
Promise
   |
   v
One eventual result
```

An Observable can represent a sequence of values over time.

``` text
Observable
   |
   +-- value
   +-- value
   +-- value
   +-- ...
```

Angular's HTTP APIs commonly use Observables.

An HTTP request normally emits one response and completes, but the
Observable abstraction also supports multi-value streams.

------------------------------------------------------------------------

# 53. Subscription

An Observable does not necessarily execute simply because it exists.

A subscription connects a consumer to the Observable.

Conceptually:

``` text
Observable
    |
    v
subscribe
    |
    v
Receive emitted values
```

Subscriptions also have lifecycle implications, particularly for
long-lived streams.

------------------------------------------------------------------------

# 54. RxJS Operators

Operators transform or control Observable streams.

Common operators include:

``` text
map
filter
switchMap
mergeMap
concatMap
catchError
debounceTime
distinctUntilChanged
tap
```

Conceptually:

``` text
Observable
    |
    v
Operator
    |
    v
Transformed Observable
```

------------------------------------------------------------------------

# 55. switchMap

`switchMap` is particularly important.

Conceptually:

``` text
New value arrives
       |
       v
Switch to new inner Observable
       |
       v
Previous inner operation can be abandoned/unsubscribed
```

It is commonly useful for search/autocomplete scenarios:

``` text
User types
   |
   v
Search API
   |
   +-- new search term
           |
           v
      switch to new request
```

------------------------------------------------------------------------

# 56. Error Handling with RxJS

HTTP/API failures can be handled through the Observable pipeline.

Conceptually:

``` text
HTTP request
    |
    v
Observable
    |
    v
catchError
    |
 +--+------+
 |         |
Recover   Re-throw/handle
```

The UI can then display an appropriate error state.

------------------------------------------------------------------------

# 57. Subject and BehaviorSubject

A Subject can act as both:

-   Observable
-   Observer

It is commonly used when application code needs to push values to
subscribers.

A BehaviorSubject additionally maintains a current value and gives a new
subscriber the current/latest value.

Conceptually:

``` text
BehaviorSubject
      |
      +-- current value
      |
      +-- Subscriber A
      +-- Subscriber B
      +-- Subscriber C
```

These concepts are often used for lightweight shared state.

------------------------------------------------------------------------

# 58. Angular Signals

Modern Angular includes **Signals** as a reactive state mechanism.

A signal represents reactive state.

Conceptually:

``` text
Signal
  |
  v
State changes
  |
  v
Dependent UI/computation updates
```

Important concepts include:

-   Signal
-   Writable signal
-   Computed value
-   Effect

Signals and RxJS solve related but different problems. Signals are
particularly useful for synchronous reactive application state, while
RxJS is designed around asynchronous/event streams and more complex
stream composition.

------------------------------------------------------------------------

# 59. Change Detection

Angular needs to know when the UI should reflect changed application
state.

This process is broadly called **change detection**.

Conceptually:

``` text
State changes
    |
    v
Angular detects relevant changes
    |
    v
Template evaluated/updated
    |
    v
DOM reflects current state
```

Modern Angular also provides more fine-grained reactive behavior through
signals.

------------------------------------------------------------------------

# 60. DOM

DOM means **Document Object Model**.

The browser represents HTML as a tree of objects.

Conceptually:

``` text
HTML
 |
 v
DOM Tree
 |
 +-- html
     |
     +-- body
         |
         +-- button
         +-- div
```

Angular updates the UI by managing the relationship between application
state and rendered DOM.

------------------------------------------------------------------------

# 61. Authentication in Angular

Authentication answers:

> Who is the user?

A common full-stack flow is:

``` text
Angular Login Form
       |
       | POST credentials
       v
ASP.NET Core API
       |
       v
Validate credentials
       |
       v
JWT/token
       |
       v
Angular
```

Angular then uses the authentication state when making protected API
requests.

------------------------------------------------------------------------

# 62. JWT

JWT is commonly used for stateless API authentication.

Conceptually:

``` text
Login
  |
  v
API
  |
  v
JWT
  |
  v
Angular
```

A later API request can include:

``` text
Authorization: Bearer <token>
```

ASP.NET Core validates the token and determines whether the request is
authenticated.

------------------------------------------------------------------------

# 63. Authentication vs Authorization

You already know this distinction from ASP.NET Core.

Authentication:

``` text
Who are you?
```

Authorization:

``` text
What are you allowed to access?
```

Angular can help enforce the user experience, but the API must enforce
actual authorization.

------------------------------------------------------------------------

# 64. HTTP Interceptors

An interceptor can observe or modify outgoing HTTP requests.

A major use case is adding an authentication token automatically.

Without an interceptor:

``` text
Component
   |
   +-- manually add token
   |
   +-- manually add token
   |
   +-- manually add token
```

With an interceptor:

``` text
Angular HTTP request
        |
        v
Interceptor
        |
        +-- add Authorization header
        |
        v
ASP.NET Core API
```

Other uses:

-   Centralized error handling
-   Logging
-   Request modification
-   Loading indicators
-   Retry behavior

------------------------------------------------------------------------

# 65. Route Guards vs Backend Authorization

These are not the same.

Route guard:

``` text
Angular
   |
   v
Should user navigate to this page?
```

Backend authorization:

``` text
ASP.NET Core
   |
   v
Is this HTTP request actually allowed?
```

Never rely only on an Angular route guard to secure sensitive data.

A user can bypass client-side UI controls and call an API directly.

The backend must enforce authorization.

------------------------------------------------------------------------

# 66. State Management

State is data representing the current application condition.

Examples:

``` text
Current user
Shopping cart
Selected employee
Theme
Notifications
Loading status
API data
```

For small applications, state can often be handled using:

-   Component state
-   Services
-   Signals
-   RxJS subjects

For larger applications, dedicated state-management patterns/libraries
may be introduced.

The important concept is:

``` text
State
  |
  v
Who owns it?
  |
  v
Who can change it?
  |
  v
Who consumes it?
```

------------------------------------------------------------------------

# 67. Models and Interfaces

Angular/TypeScript applications commonly define interfaces describing
API data.

Conceptually:

``` text
Employee
|
+-- id
+-- name
+-- department
+-- salary
```

An interface primarily describes the shape of an object at compile time.

It is not the same as an ASP.NET Core entity.

For example:

``` text
Database Entity
       |
       v
ASP.NET DTO
       |
       v
JSON
       |
       v
TypeScript Interface
```

------------------------------------------------------------------------

# 68. Entity vs DTO vs Frontend Model

These are different concepts.

``` text
SQL Database
    |
    v
EF Core Entity
    |
    v
ASP.NET Core DTO
    |
    v
JSON
    |
    v
Angular TypeScript Interface
```

Do not assume that one class must be shared across all layers.

Each layer can have its own representation.

------------------------------------------------------------------------

# 69. Angular Architecture for Your .NET Stack

A clean full-stack structure can look like:

``` text
                    Browser
                       |
                       v
                  Angular App
                       |
        +--------------+--------------+
        |              |              |
   Components       Services       Router
        |              |              |
        |              v              |
        |          HttpClient         |
        |              |              |
        +--------------+--------------+
                       |
                    HTTP/JSON
                       |
                       v
             ASP.NET Core Web API
                       |
                Controllers
                       |
                    Services
                       |
                  DTOs / EF Core
                       |
                       v
                  SQL Server
```

------------------------------------------------------------------------

# 70. Complete CRUD Flow

Consider Employee CRUD.

## Read

``` text
EmployeeListComponent
       |
       v
EmployeeService
       |
       v
HttpClient
       |
       | GET
       v
/api/employees
       |
       v
ASP.NET Controller
       |
       v
Service
       |
       v
EF Core
       |
       v
SQL Server
```

Response travels back as JSON.

------------------------------------------------------------------------

## Create

``` text
Angular Form
     |
     v
Component
     |
     v
EmployeeService
     |
     | POST
     v
ASP.NET Core
     |
     v
Validation
     |
     v
Service
     |
     v
EF Core
     |
     v
Database
```

------------------------------------------------------------------------

## Update

``` text
Angular Edit Form
       |
       v
PUT/PATCH
       |
       v
ASP.NET Core
       |
       v
EF Core
       |
       v
Database
```

------------------------------------------------------------------------

## Delete

``` text
Delete button
      |
      v
Component
      |
      v
Service
      |
      v
DELETE /api/employees/10
      |
      v
ASP.NET Core
      |
      v
Database
```

------------------------------------------------------------------------

# 71. Error Handling

Errors can occur at multiple levels.

``` text
Angular
   |
   | HTTP request
   v
Network
   |
   v
ASP.NET Core
   |
   v
Database
```

Examples:

``` text
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
409 Conflict
500 Internal Server Error
```

Angular should interpret the response and present an appropriate UI
state.

The backend should return consistent error responses.

------------------------------------------------------------------------

# 72. Loading State

API calls are asynchronous.

The UI often needs states such as:

``` text
Loading
   |
   +-- Success
   |
   +-- Error
```

For example:

``` text
isLoading = true

       |
       v
API request

       |
   +---+---+
   |       |
Success   Error
   |       |
   v       v
Display   Error
data      message
```

This is an important real-world frontend concept.

------------------------------------------------------------------------

# 73. Environment Configuration

Applications commonly have different environments:

``` text
Development
Testing
Staging
Production
```

API URLs and other environment-specific configuration should not be
hardcoded throughout components.

Conceptually:

``` text
Angular
   |
   v
Environment/configuration
   |
   +-- Development API URL
   +-- Production API URL
```

The exact configuration mechanism varies by Angular version and project
setup.

------------------------------------------------------------------------

# 74. Build Process

Angular source code is not simply shipped exactly as written.

Conceptually:

``` text
TypeScript
HTML
CSS
Assets
   |
   v
Angular build system
   |
   +-- Compile
   +-- Bundle
   +-- Optimize
   +-- Minify
   +-- Process assets
   |
   v
Production browser files
```

The browser ultimately receives the built application assets.

------------------------------------------------------------------------

# 75. Angular and Browser

At runtime:

``` text
Browser
 |
 +-- HTML
 +-- JavaScript
 +-- CSS
 +-- Angular runtime/application code
```

Angular executes client-side logic and manages the application's UI.

------------------------------------------------------------------------

# 76. Standalone Components

Modern Angular applications commonly use **standalone components**.

Older Angular applications heavily relied on `NgModule`.

Historically:

``` text
NgModule
   |
   +-- Components
   +-- Directives
   +-- Pipes
   +-- Providers
```

Modern Angular allows components and other entities to be configured
without requiring every feature to belong to an NgModule.

You should understand both because real projects may contain either
architecture.

------------------------------------------------------------------------

# 77. NgModule

NgModule is Angular's older module-based organization mechanism.

Important concepts:

-   Declarations
-   Imports
-   Providers
-   Exports

Many legacy and existing enterprise applications still use NgModules.

For modern Angular, standalone APIs are increasingly important.

------------------------------------------------------------------------

# 78. Dependency Graph

A real Angular application is essentially a dependency graph.

For example:

``` text
EmployeeListComponent
        |
        v
EmployeeService
        |
        v
HttpClient
        |
        v
API
```

Another component might use:

``` text
EmployeeDetailsComponent
        |
        v
EmployeeService
```

The same service can therefore be reused.

------------------------------------------------------------------------

# 79. Component vs Service vs Directive vs Pipe

  Concept           Primary responsibility
  ----------------- ----------------------------------------
  Component         UI + UI state/behavior
  Service           Reusable application logic/data access
  Directive         Modify element behavior/structure
  Pipe              Transform values for display
  Guard             Control route navigation
  Interceptor       Intercept HTTP communication
  Model/Interface   Describe data shape

This distinction is extremely useful when reading an Angular project.

------------------------------------------------------------------------

# 80. Common Angular Request Flow

When a user clicks a button:

``` text
User
 |
 v
Template
 |
 v
Event Binding
 |
 v
Component
 |
 v
Service
 |
 v
HttpClient
 |
 v
Interceptor
 |
 v
HTTP
 |
 v
ASP.NET Core API
 |
 v
Controller
 |
 v
Service
 |
 v
EF Core
 |
 v
SQL Server
```

Response:

``` text
SQL Server
 |
 v
EF Core
 |
 v
ASP.NET Service
 |
 v
Controller
 |
 v
JSON
 |
 v
HTTP
 |
 v
Interceptor
 |
 v
HttpClient
 |
 v
Service
 |
 v
Component
 |
 v
Template
 |
 v
User
```

This is the most important full-stack mental model.

------------------------------------------------------------------------

# 81. Angular Authentication Request Flow

``` text
LoginComponent
      |
      v
AuthService
      |
      v
HttpClient
      |
      v
POST /api/auth/login
      |
      v
ASP.NET Core
      |
      v
Authentication
      |
      v
JWT
      |
      v
Angular
```

Later:

``` text
Component
    |
    v
HttpClient
    |
    v
HTTP Interceptor
    |
    +-- Authorization: Bearer JWT
    |
    v
ASP.NET Core
    |
    v
Authentication middleware
    |
    v
Authorization
    |
    v
Controller
```

------------------------------------------------------------------------

# 82. Angular Security Principles

Important principles:

1.  Never trust client-side authorization.
2.  Protect APIs on the backend.
3.  Validate data on the backend.
4.  Do not expose secrets in Angular source.
5.  Use HTTPS in production.
6.  Handle authentication tokens carefully.
7.  Avoid putting sensitive information into client-side code.
8.  Use appropriate browser security controls.

Angular is public client-side code. Anything shipped to the browser
should be treated as potentially visible to the user.

------------------------------------------------------------------------

# 83. Performance Concepts

Important Angular performance concepts include:

-   Lazy loading
-   Efficient component design
-   Avoiding unnecessary work in templates
-   Proper list rendering
-   Change detection strategy
-   Signals
-   Caching where appropriate
-   Reducing bundle size
-   Image/asset optimization
-   Avoiding unnecessary API calls

You do not need to optimize prematurely. First understand the
architecture.

------------------------------------------------------------------------

# 84. Testing Concepts

Angular applications can be tested at different levels.

``` text
Unit Test
   |
   +-- Component
   +-- Service
   +-- Pipe
   +-- Utility

Integration/TestBed-style testing
   |
   +-- Multiple Angular pieces

End-to-End
   |
   +-- Complete browser workflow
```

Testing tools and recommended approaches can change with Angular
versions, so project-specific tooling should be checked when you begin a
real project.

------------------------------------------------------------------------

# 85. Angular vs React --- Basic Concept

Angular is a full frontend framework.

React is primarily a UI library/ecosystem.

Angular provides a more integrated set of concepts:

``` text
Angular
|
+-- Components
+-- Router
+-- DI
+-- Forms
+-- HTTP
+-- RxJS integration
+-- Framework tooling
```

With React, teams commonly select additional libraries/tools for several
of these concerns.

This is a broad architectural distinction rather than a statement that
one is universally better.

------------------------------------------------------------------------

# 86. Angular Learning Priority

For an ASP.NET Core developer, prioritize:

``` text
1. Components
       ↓
2. Templates
       ↓
3. Data Binding
       ↓
4. Directives
       ↓
5. Services
       ↓
6. Dependency Injection
       ↓
7. Routing
       ↓
8. Forms
       ↓
9. HttpClient
       ↓
10. RxJS / Observables
       ↓
11. Authentication
       ↓
12. Interceptors
       ↓
13. Guards
       ↓
14. Signals / State
       ↓
15. Application architecture
```

------------------------------------------------------------------------

# 87. What You Should Be Able to Explain in an Interview

After studying these concepts, you should be able to answer:

### Angular fundamentals

-   What is Angular?
-   Why is Angular used?
-   What is an SPA?
-   Angular vs AngularJS?
-   Angular vs ASP.NET MVC?
-   Why does Angular use TypeScript?

### Components

-   What is a component?
-   What is a component template?
-   What is component state?
-   What is component hierarchy?
-   What are lifecycle hooks?
-   What is `ngOnInit`?
-   What is `ngOnDestroy`?
-   What are `@Input` and `@Output`?

### Templates

-   What is interpolation?
-   What is property binding?
-   What is event binding?
-   What is two-way binding?
-   What are directives?
-   What are pipes?

### Architecture

-   Why use services?
-   How does Angular DI work?
-   What is the Angular injector?
-   What is routing?
-   What are route parameters?
-   What is lazy loading?
-   What are route guards?

### Forms

-   Template-driven vs reactive forms?
-   What is FormControl?
-   What is FormGroup?
-   What is FormArray?
-   How does validation work?

### API integration

-   What is HttpClient?
-   How does Angular communicate with ASP.NET Core?
-   What is CORS?
-   What are Observables?
-   Observable vs Promise?
-   What is a subscription?
-   What are RxJS operators?
-   What is an interceptor?

### Security

-   Authentication vs authorization?
-   How does JWT authentication work?
-   Why are route guards not sufficient for API security?
-   Why must authorization be enforced by ASP.NET Core?

------------------------------------------------------------------------

# 88. Final Mental Model

If you remember only one architecture diagram, remember this:

``` text
                         USER
                          |
                          v
                    ANGULAR UI
                          |
                    COMPONENTS
                          |
             +------------+------------+
             |                         |
         Templates                  Services
             |                         |
             |                      HttpClient
             |                         |
             +------------+------------+
                          |
                       Router
                          |
                    HTTP / JSON
                          |
                    Interceptor
                          |
                          v
              ASP.NET CORE WEB API
                          |
                     Controllers
                          |
                       DTOs
                          |
                      Services
                          |
                       EF Core
                          |
                          v
                     SQL Server
```

And the major Angular concepts fit around that architecture:

``` text
                         ANGULAR
                            |
       +--------------------+--------------------+
       |                    |                    |
  Components            Services             Router
       |                    |                    |
  Templates                DI                 Guards
       |                    |                    |
 Data Binding          HttpClient          Lazy Loading
       |                    |                    |
 Directives              RxJS
       |                    |
     Pipes               Signals
       |
     Forms
```

The key idea is:

> **Angular manages the client-side application and UI; ASP.NET Core Web
> API manages server-side application logic and data.**

Once you understand that boundary, the individual Angular concepts
become much easier to place.
