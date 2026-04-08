---
title: "Builder"
tags: ["AL","Decoupling","Readability","Testability","Extendability"]
categories: ["Pattern", "Creational"]
---
 
_Created by Erich Gamma, Richard Helm, Ralph Johnson, John Vlissides (Gang of Four), Described by Elton Werkmeister_
 
## Abstract
 
The *Builder Pattern* is one of 23 remarkable design patterns described in the book "Design Patterns". Its intent is to make the construction of complex objects more elegant and less complex, while solving various other object creation problems.
 
With the newly introduced *"this"* operator in Version 14 of AL, the Builder Pattern finally found its way to BC. Let me show you how it works.
 
## Context
 
Whenever you have an object with a lot of fields or configuration options that need to be set in a specific or flexible way,
the Builder Pattern can help simplify the creation process.  
It is especially useful when the object initialization logic would otherwise lead to complex constructors or scattered setup code.
 
## Problem
 
The Builder Pattern helps to solve the following problems:  
 
### Object signatures are often overloaded with parameters...
certain procedure demand a long list of parameters, which can reduce readability and increase the risk of passing values in the wrong order,
especially when several parameters share the same data type.
 
### Objects might have parameters that are actually optional...
Not every object requires all fields to be set in every context.
Handling optional parameters often leads to overloaded code or conditional initialization scattered across the application.
 
### Object creation frequently involves complex logic...
Instantiating some objects requires multiple setup steps, conditional logic, or validation rules.
Without a clear structure, this logic often ends up in large, blocks of code that are hard to test and maintain.
 
## Description
 
The Builder Pattern is one of the creational design patterns.
A so-called builder simplifies the creation of objects by allowing the user to assemble objects step by step.  
It exposes several small, focused methods that store data in global variables within the builder.  
A key feature is that these methods return the builder itself, enabling method chaining for fluent and readable object construction.
 
### The Pattern
 
This pattern is particularly easy to understand and implement because of its clear structure.  
Imagine this very simple example of a SMTP Mail setup table:
 
| Field Name        | Type       |
| ----------------- | ---------- |
| `Server`          | Text\[100] |
| `Port`            | Integer    |
| `UserID`          | Text\[100] |
| `UserPassword`    | SecretText |
| `AuthRequired`    | Boolean    |
| `ConnectionType`  | Enum Connection Type |
| `IsDefaultConfig` | Boolean    |
 
Without the builder pattern, a "CreateSmtpSetup" procedure could look something like this:
 
```C#
procedure CreateSmtpSetup(
    Server: Text[100];
    Port: Integer;
    UserID: Text[100];
    UserPassword: SecretText;
    AuthRequired: Boolean;
    UseSSL: Boolean;
    IsDefaultConfig: Boolean
): Record "SMTP Mail Setup"
var
    Setup: Record "SMTP Mail Setup";
begin
    Setup.Init();
    Setup.Server := Server;
    Setup.Port := Port;
    Setup.AuthRequired := AuthRequired;
    Setup.UserID := UserID;
    if UseSSL then
        Setup.ConnectionType := Enum::ConnectionType::SSL
    else
        Setup.ConnectionType := Enum::ConnectionType::Other;
    Setup.UseSSL := UseSSL
    Setup.IsDefaultConfig := IsDefaultConfig;
    exit(Setup);
end;
```
 
The call to that procedure like this:
 
```C#
procedure XYZ()
var
    SmtpSetup: Record "SMTP Mail Setup";
begin
    SmtpSetup := CreateSmtpSetup(
        'smtp.office365.com',
        587,
        'admin@yourdomain.com',
        'JohnDoe12345',
        true,
        true,
        true
    );
    SmtpSetup.Insert(true);
end;
```
 
As you can see, both the procedure itself and the way it is called can quickly become confusing and hard to maintain.
Let me show you the definition of the corresponding builder.
 
```C#
Codeunit 99999 "SmtpSetupBuilder" {
    var
        SmtpSetupProduct: Record SmtpSetup;
 
    procedure Init(): Codeunit "SmtpSetupBuilder" begin
        SmtpSetupProduct.Init();
    end;
 
    procedure Server(Value: Text[100]): Codeunit "SmtpSetupBuilder" begin
        SmtpSetupProduct.Validate(Server, Value);
        exit(this);
    end;
 
    procedure Port(Value: Integer): Codeunit "SmtpSetupBuilder" begin
        SmtpSetupProduct.Validate(Port, Value);
        exit(this);
    end;
 
    procedure UserID(Value: Text[100]): Codeunit "SmtpSetupBuilder" begin
        SmtpSetupProduct.Validate(UserID, Value);
        exit(this);
    end;
 
    procedure UserPassword(Value: SecretText): Codeunit "SmtpSetupBuilder" begin
        SmtpSetupProduct.Validate(UserPassword, Value);
        exit(this);
    end;
 
    procedure AuthRequired(Value: Boolean): Codeunit "SmtpSetupBuilder" begin
        SmtpSetupProduct.Validate(AuthRequired, Value);
        exit(this);
    end;
 
    procedure ConnectionType(Value: Enum ConnectionType): Codeunit "SmtpSetupBuilder" begin
            SmtpSetupProduct.Validate(ConnectionType, Value);
            exit(this);
    end;
 
    procedure IsDefaultConfig(Value: Boolean): Codeunit "SmtpSetupBuilder" begin
        SmtpSetupProduct.Validate(IsDefaultConfig, Value);
        exit(this);
    end;
 
    procedure Build(var SmtpSetup: Record SmtpSetup): Codeunit "SmtpSetupBuilder" begin
        SmtpSetup := SmtpSetupProduct;
        exit(this);
    end;
}
```
 
You should note some things in the builder-codeunit above:
 
- The builder has a global record variable storing the current building-state called "product".
 
- Every field of the table "SmtpSetupBuilder" has its own setter-procedure that is named like the field-caption.
 
- The setter-procedures return the codeunit itself with the this-operator.
 
- The builder has a init-procedure that initializes a new product.
 
- The builder has a build-procedure that copies the building-product into a referenced argument.
 
Again. To use the builder you call the procedures like this:
 
```C#
procedure XYZ()
var
    SmtpSetupBuilder: Codeunit "SmtpSetupBuilder";
    SmtpSetup: Record "SMTP Mail Setup";
begin
    SmtpSetupBuilder
    .Init()
    .Server('smtp.office365.com')
    .Port(587)
    .UserID('admin@yourdomain.com')
    .UserPassword('JohnDoe12345')
    .AuthRequired(true)
    .ConnectionType(Enum::ConnectionType::SSL)
    .IsDefaultConfig(true)
    .Build(SmtpSetup);
    SmtpSetup.Insert(true);
end;
```
 
Constructing complex objects was never so easy and readable. You have everything capsuled in its own procedure, you know exactly what is being set and have full control of how it is constructed.
 
Because the current state of the building-object is stored inside the instance, you can even omit the setting of the same fields, when creating similar objects like this:
 
```C#
procedure XYZ()
var
    SmtpSetupBuilder: Codeunit "SmtpSetupBuilder";
    SmtpSetup1: Record "SMTP Mail Setup";
    SmtpSetup2: Record "SMTP Mail Setup";
    SmtpSetup3: Record "SMTP Mail Setup";
begin
    // Initialize the fields, that are the same
    // for all constructing objects.
    SmtpSetupBuilder
    .Init()
    .Server('smtp.office365.com')
    .Port(587)
    .AuthRequired(true)
    .ConnectionType(Enum::ConnectionType::SSL)
    .IsDefaultConfig(true);
 
    // Only set the changing fields for each needed
    // object.
    SmtpSetupBuilder
    .UserID('admin@yourdomain.com')
    .UserPassword('JohnDoe12345')
    .Build(SmtpSetup1);
 
    SmtpSetupBuilder
    .UserID('user1@yourdomain.com')
    .UserPassword('PasswordIsSafe123')
    .Build(SmtpSetup2);
 
 
    SmtpSetupBuilder
    .UserID('user2@yourdomain.com')
    .UserPassword('12345')
    .Build(SmtpSetup3);
 
    // (...)
end;
```
 
I think this is especially useful, when used in automatic tests while constructing test-data.
 
## Usage

The textbuilder is a nice example of the builder pattern implemented in AL. 
 
## Benefits
 
Again the nice feats that the Builder-Pattern provides you, when implemented:
 
The builder ...
 
### ...Improves readability by avoiding long parameter lists
Instead of passing many arguments to a single procedure, the Builder Pattern allows each value to be set individually with clearly named methods, making the code easier to read and understand.
 
### ...Handles optional parameters gracefully
Only the necessary values need to be set explicitly. Optional fields can be left out, defaulted, or conditionally assigned without overloading methods or writing additional logic.
 
### ...Encapsulates complex creation logic in one place
All setup steps, validations, and conditionals are contained within the builder object, keeping the main business logic clean and focused while promoting reusability.
 
### ...Promotes immutability and consistency
The builder ensures that objects are created in a controlled way, reducing the chance of invalid or inconsistent states.
 
### ...Enhances testability
By centralizing and separating the creation logic, it's easier to write unit tests for how objects are constructed, especially in complex scenarios.
 
## When not to use

In some cases the usage of the builder pattern could not be beneficial. Like for example where...

### ...the object is too simple.
If the object has only a few fields or doesn't require conditional setup logic, the builder introduces unnecessary complexity.

### ... performance is critical.
Builders rely on method chaining and object encapsulation, which can introduce a slight performance overhead. This could become a problem when you develop performance-critical batch jobs that process thousands of records.

### ... the implementation generally leads to overengineering and bloated abstractions
Applying the builder pattern everywhere may lead to overengineering and bloated abstractions.
A good rule of thumb is to use the pattern only when it adds real value — not just for the sake of applying a pattern.
 
## Snippets
 
An example snippet is provided [here](/content\docs\patterns\builder-pattern\snippets\AL.json).
 
## List of references
 
- Design Patterns: Elements of Reusable Object-Oriented Software. Addison Wesley. ISBN 0-201-63361-2.
 
- [Refactoring-Guru: Builder](https://refactoring.guru/design-patterns/builder) - A very nice page for different kinds design patterns with beautiful examples.