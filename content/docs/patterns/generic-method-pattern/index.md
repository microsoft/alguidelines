---
title: "Generic Method"
tags: ["AL","Decoupling","Readability","Testability","Extendability"]
categories: ["Pattern"]
---

_Created by Gary Winter (Cloud Ready Software), Described by waldo (iFacto Business Solutions | Dynex)_

## Abstract

The goal of this pattern is to facilitate a lot of things in one single awesome way of writing code.  If you apply this pattern as a general pattern, you'll implement:

- Extensibility
- Decoupling
- Readability
- Testability
- Encapsulation

## Context

Whenever you have to write an isolated piece of business logic, this pattern should be applicable.

## Problem

Usually, when you ask people where to place code, they all have their own opinion: on a table or page, or in some kind of codeunit library where lots of functions will be placed, or .. .  In fact, does it matter?  It sure does, because in many cases, the code simply isn't maintainable, let alone extendable or testable.

## Description

What if we have some kind of "standard way" to always write our code.  The _Generic Method Pattern_ is kind of like what it says: a generic way to implement a method.

### What is a method?

Well, a method is _a significant piece of business logic_ - maybe best explained by some examples:

- Posting a document
- Any button on a page that executes business logic
- a batch run to send emails
- ...

In a way, except "data validation", most of the things we write in our daily life, are methods.

### The Pattern

**One method, one codeunit**
The idea is to put the code in one _encapsulated_ codeunit with the purpose to have all the code in that one codeunit function for that one method.  This way, the codeunit will stay relatively small and readable.

Let me start by showing an example, so you can refer to this complete example during the rest of the article:

```AL
codeunit 53100 "WLD BlockCustomer Meth"
{
    internal procedure BlockCustomer(var Cust: Record Customer; HideDialog: Boolean)
    var
        IsHandled: Boolean;
    begin
        if not ConfirmBlockCustomer(HideDialog) then 
            exit;
        OnBeforeBlockCustomer(Cust, IsHandled);
        DoBlockCustomer(Cust, IsHandled);
        OnAfterBlockCustomer(Cust);
        AcknowledgeBlockCustomer(HideDialog)
    end;

    local procedure DoBlockCustomer(var Cust: Record Customer; IsHandled: Boolean);
    begin
        if IsHandled then
            exit;

        Cust.Blocked := Cust.Blocked::All;
        Cust.Modify(true);
    end;

    local procedure ConfirmBlockCustomer(HideDialog: Boolean): Boolean
    var
        ConfirmManagement: Codeunit "Confirm Management";
        ConfirmQst: Label 'Are you sure?';
        DefaultAnswer: Boolean;
    begin
        DefaultAnswer := true;

        if HideDialog then 
            exit(DefaultAnswer);
        exit(ConfirmManagement.GetResponseOrDefault(ConfirmQst, DefaultAnswer));
    end;

    local procedure AcknowledgeBlockCustomer(HideDialog: Boolean)
    var
        AcknowledgeMsg: Label 'You successfully executed "BlockCustomer"';
    begin
        if not GuiAllowed or HideDialog then 
            exit;
        Message(AcknowledgeMsg);
    end;

    [IntegrationEvent(false, false)]
    local procedure OnBeforeBlockCustomer(var Cust: Record Customer; var IsHandled: Boolean);
    begin
    end;

    [IntegrationEvent(false, false)]
    local procedure OnAfterBlockCustomer(var Cust: Record Customer);
    begin
    end;
}
```

Within that codeunit, the pattern is always the same:

- One public (internal) procedure
- The rest is always local

So, from outside the codeunit, there is only one clear entrypoint: that one  (public) internal function with its parameters.

The **pattern** within the codeunit exists of a few layers:

- The UI layer
- The Event layer
- The method layer

_The UI layer_
The UI layer takes care of the UI, obviously.  What is important in this case, is that you always make sure that there is a "HideDialog" parameter that the business logic can use to still decide whether to use the dialog or not.

These are the UI Layer parts, where you see the public function gets the HideDialog, and passes it to the UI-related procedures, where the business logic for showing the UI takes place.  Also, the default answer of the confirmation is handled there as well (what if the business logic calls this method with HideDialog to "true").

```AL
codeunit 53100 "WLD BlockCustomer Meth"
{
    internal procedure BlockCustomer(var Cust: Record Customer; HideDialog: Boolean)
    var
        IsHandled: Boolean;
    begin
        if not ConfirmBlockCustomer(HideDialog) then 
            exit;
        ...
        AcknowledgeBlockCustomer(HideDialog)
    end;
...

    local procedure ConfirmBlockCustomer(HideDialog: Boolean): Boolean
    var
        ConfirmManagement: Codeunit "Confirm Management";
        ConfirmQst: Label 'Are you sure?';
        DefaultAnswer: Boolean;
    begin
        DefaultAnswer := true;

        if HideDialog then 
            exit(DefaultAnswer);
        exit(ConfirmManagement.GetResponseOrDefault(ConfirmQst, DefaultAnswer));
    end;

    local procedure AcknowledgeBlockCustomer(HideDialog: Boolean)
    var
        AcknowledgeMsg: Label 'You successfully executed "BlockCustomer"';
    begin
        if not GuiAllowed or HideDialog then 
            exit;
        Message(AcknowledgeMsg);
    end;
...
}
```

_The Event layer_
This layer is going to add flexibility to any app that has a dependency on this app.  By default, the pattern always foresees an `OnBefore` and an `OnAfter` event.  

This is the relevant code for the event layer:

```AL
codeunit 53100 "WLD BlockCustomer Meth"
{
    internal procedure BlockCustomer(var Cust: Record Customer; HideDialog: Boolean)
    var
        IsHandled: Boolean;
    begin
        ...
        OnBeforeBlockCustomer(Cust, IsHandled);
        ...
        OnAfterBlockCustomer(Cust);
        ...
    end;
...    
    [IntegrationEvent(false, false)]
    local procedure OnBeforeBlockCustomer(var Cust: Record Customer; var IsHandled: Boolean);
    begin
    end;

    [IntegrationEvent(false, false)]
    local procedure OnAfterBlockCustomer(var Cust: Record Customer);
    begin
    end;
}
```

_The method layer_
The last layer is obviously where the business logic will be written.

The relevant part is:

```AL
codeunit 53100 "WLD BlockCustomer Meth"
{
    internal procedure BlockCustomer(var Cust: Record Customer; HideDialog: Boolean);
    var
        IsHandled: Boolean;
    begin
        ...
        DoBlockCustomer(Cust, IsHandled);
        ...
    end;

    local procedure DoBlockCustomer(var Cust: Record Customer; IsHandled: Boolean);
    begin
        if IsHandled then
            exit;

        Cust.Blocked := Cust.Blocked::All;
        Cust.Modify(true);
    end;
...
}
```

Usually indicated with a "do"-function, the business logic takes place in that procedure.  Obviously, when you have a decent amount of code, it's recommended that you make it readable by applying all the Best Practices in terms of readability in the codeunit.  Though, a few pointers here:

- keep the [cyclomatic complexity](https://en.wikipedia.org/wiki/Cyclomatic_complexity) low
  - one line (function call) after an IF-clause
  - one line (function call) after a repeat
- readable function calls
- ...

**Call out to the method from a table**

Now, there is a reason that the one global procedure in the method-codeunit is `internal`.  We shouldn't be calling this procedure directly.  We should be calling it through it's "class" (usually, a table can be seen as a class).  That means, we would have to create a public (not internal) procedure on table-level, which can be used in the business logic.

In our example above, this could be that table extension:

```AL
tableextension 53100 "Customer Ext BASE" extends Customer
{
    procedure BlockCustomer(HideDialog: Boolean)
    var
        WLDBlockCustomerMeth: Codeunit "WLD BlockCustomer Meth";
    begin
        WLDBlockCustomerMeth.BlockCustomer(Rec, HideDialog);
    end;

    procedure BlockCustomer()
    begin
        BlockCustomer(false);
    end;
}
```

This practice improves readability.  In fact, by doing this, you just extended the suggestions-list in VSCode (IntelliSense) indicating a new method that your class can do.  This is very convenient for the developer that might need your new method.

_Note - it could very well be that there simply isn't any table that can act as a class for our method.  In that case, you could use a codeunit as well._

**Naming Conventions**

You might have noticed that the naming of our method is quite strict:

- codeunit name: `WLD BlockCustomer Meth`
- internal proc: `BlockCustomer`
- do-procedure: `DoBlockCustomer`

It is important to align these namings.  It indicates that the codeunit only does one thing (remember: encapsulation), and it improves searchability from outside the codeunit (for example when you're searching symbols or something).

## Usage

Currently, there is no usage of this pattern in the BaseApp.  

The pattern has a main advantage in an ISV product, just because of the decoupling and extensibility.  Although, I have seen many occasions where parts of the pattern was useful on PTE's as well.  You simply never know if ever at the customer site, there is going to be another partner that needs to create its own PTE, and has to depend on yours.  So I'd say, this pattern is everywhere applicable, no matter the type of the app.

## Benefits

As I said, it will facilitate a lot of advantages.  Let's explain a bit more in depth:

### Extensibility

Thanks to the _event layer_, by applying this pattern for all methods, we will automatically have the bare minimum of events that we need to hook into a method: the `OnBefore-` and the `OnAfter`.  Of course it would make sense to even add more events to the method when appropriate (eg, when you're inserting a record in a table, it might be interesting to also raise an event just before you call the insert).

### Decoupling

Thanks to these same events, and the fact the pattern foresees a handler as well, we are able to "decouple" our method as well.  What do I mean with that?  Well, we can simply subscribe to the `OnBefore`event, and set `IsHandled` to `true`. This means it will never execute the do-procedure, which means, the original procedure/method/business logic is "decoupled".

We can use this obviously for implementing our own method (a new way to accomplish this method), or to disable the method by simply subscribing to it, and only providing the `IsHandled := true` in our subscriber.  However, there are many more usages where we can use this for.  

Maybe one more example:
if you would apply this pattern to your product, at the customer, you'll be able to hotfix your methods simply by decoupling them and fixing the method instead of waiting for a hotfix from the hotfix-departement.

This gives a lot of flexibility.

### Readability

When we talk about readability, we actually talk about the part where we expose our method on the class.  The rule is: never call the codeunit, but only from one place: from its "class" - or in BC terms: its table (or codeunit).

In terms of readability, that means that intellisense comes into play.  In stead of:

```AL
Codeunit.Run(Codeunit::"Sales-Post", SalesHeader);
```

you simply get

```AL
SalesHeader.Post();
```

THAT is readable.  The previous is not!  That is just something we got used to.

### Testability

There are two things in terms of testability where this pattern helps a lot.

_Unit testing_
You can interpret "unit testing" very broadly.  But just imagine: when you're building your software entirely out of "methods" - which means: when you'd build your software entirely with this "Generic Method Pattern".  Now, the list of methods, are all the units that you need to test: if you test all your methods, you kind of like test the majority of your software, right?

So you could simply set up rules in your company like: EVERY method needs a test-codeunit.  And even more: since every method only has one global function - it's pretty easy to know the context, and all the flavors to test your method.

The pattern describes the tests that needs to be written.

_Disabling methods_
Coming back to the "decoupling" part - in tests, you actually might need it more than you realize.  Just imagine: you want to test method 1, but method 2 comes in the way by interfering with configurations that you need to do, or UI that is popping up, while it could be completely pointless.  
Solution: simply - within your test-codeunit - subscribe (with a manual subscriber) to method 2, set `IsHandled` to `false` - done!

### Encapsulation

Don't underestimate the power of the encapsulation part of this pattern.  One of the first questions that people ask themselves when reading into this pattern is: "_isn't it going to consume all my codeunit-id's_" or "_so many codeunits, that can't be readable, right?_".  
The fact that the functionality of one method is encapsulated in one codeunit is very powerful.  You'll avoid [Boat anchors](https://sourcemaking.com/antipatterns/boat-anchor) simply because because, thanks to the encapsulation, there is a limited amount of code in the codeunit, of course.  
And because of that, it so much more maintainable, upgradable, readable, .. .  Only advantages.

## When not to use

However, there are occasions where you can't use this pattern.  Just imagine if you'd put EVERY SINGE LINE of code in a method, including the simple validation code on a table, or library-functions like in helper codeunits.  That would obviously not make any sense.

So all I can say is: use your common sense.  

One example: set the bar at "validation code": any code that is solely there to facilitate data integrity doesn't belong in method codeunits.

Another tip might be: don't let the amount of codelines trick you in deciding to _not_ use this pattern: when it's a method, it's a method.  When it makes sense to be able to extend, decouple, .. then this pattern can help.

## Snippets

[waldo's CRS AL Language Extension](https://marketplace.visualstudio.com/items?itemName=waldo.crs-al-language-extension) contains snippets that help you in setting up the boiler plate code in a matter of seconds.

The snippets are:

- `tcodeunitMethodWithoutUIwaldo`
- `tcodeunitMethodWithUIwaldo`

## List of references

There have been a number of occasions where people have been sharing this pattern.  Here is one:

{{< youtube id="CWpaD9RUa6U" yt_start="1516" >}}
