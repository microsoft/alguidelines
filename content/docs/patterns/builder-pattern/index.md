---
title: "Builder"
tags: ["AL","Decoupling","Readability","Testability","Extendability"]
categories: ["Pattern", "Creational"]
---

_Created by Erich Gamma, Richard Helm, Ralph Johnson, John Vlissides (Gang of Four), Described by Elton Werkmeister_

## Abstract

The *Builder Pattern* is one of 23 remarkable design patterns described in the book "Design Patterns". Its intent is to make the costruction of complex objects in a beautiful way not so complex, while solving various other object creation problems.

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

<!-- The Builder Pattern provides these feats:  

### Improved readability by avoiding long parameter lists
Instead of passing many arguments to a single procedure, the Builder Pattern allows each value to be set individually with clearly named methods, making the code easier to read and understand.

Handles optional parameters gracefully
Only the necessary values need to be set explicitly. Optional fields can be left out, defaulted, or conditionally assigned without overloading methods or writing additional logic.

Encapsulates complex creation logic in one place
All setup steps, validations, and conditionals are contained within the builder object, keeping the main business logic clean and focused while promoting reusability.

Promotes immutability and consistency
The builder ensures that objects are created in a controlled way, reducing the chance of invalid or inconsistent states.

Enhances testability
By centralizing and separating the creation logic, it's easier to write unit tests for how objects are constructed, especially in complex scenarios. -->

### The Pattern

The Builder Pattern is easy to implement and understand.  


This pattern is particularly easy to understand and implement because of its clear structure.  
Imagine this very simple example of a SMTP Mail setup table, that prove the point of the pattern:

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
    Setup."Server" := Server;
    Setup."Port" := Port;
    Setup."AuthRequired" := AuthRequired;
    Setup."UserID" := UserID;
    if UseSSL then
        Setup."ConnectionType" := Enum::ConnectionType::SSL
    else
        Setup."ConnectionType" := Enum::ConnectionType::Other;
    Setup."UseSSL" := UseSSL
    Setup."IsDefaultConfig" := IsDefaultConfig;
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

You probably see that the procedure itself as well as the calling of said procedure is or could become very confusing and tedious.  
Let me show you the definition of the corresponding builder.

```C#
Codeunit 99999 "SmtpSetupBuilder" {
    var
        SmtpSetupProduct: Record SmtpSetup;

    procedure Init(): Codeunit "SmtpSetupBuilder" begin
        SmtpSetupProduct.Init();
    end;

    procedure Server(Value: Text[100]): Codeunit "SmtpSetupBuilder" begin
        SmtpSetupProduct := Value;
        exit(this);
    end;

    procedure Port(Value: Integer): Codeunit "SmtpSetupBuilder" begin
        SmtpSetupProduct := Value;
        exit(this);
    end;

    procedure UserID(Value: Text[100]): Codeunit "SmtpSetupBuilder" begin
        SmtpSetupProduct := Value;
        exit(this);
    end;

    procedure UserPassword(Value: SecretText): Codeunit "SmtpSetupBuilder" begin
        SmtpSetupProduct := Value;
        exit(this);
    end;

    procedure AuthRequired(Value: Boolean): Codeunit "SmtpSetupBuilder" begin
        SmtpSetupProduct := Value;
        exit(this);
    end;

    procedure ConnectionType(Value: Enum ConnectionType): Codeunit "SmtpSetupBuilder" begin
            SmtpSetupProduct := Value;
            exit(this);
    end;

    procedure IsDefaultConfig(Value: Boolean): Codeunit "SmtpSetupBuilder" begin
        SmtpSetupProduct := Value;
        exit(this);
    end;

    procedure Build(var SmtpSetup: Record SmtpSetup): Codeunit "SmtpSetupBuilder" begin
        SmtpSetup := SmtpSetupProduct;
        exit(this);
    end;
}
```


```C#
procedure XYZ()
var
    SmtpSetupBuilder: Codeunit "SmtpSetupBuilder";
    SmtpSetup: Record "SMTP Mail Setup";
begin
    SmtpSetupBuilder
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