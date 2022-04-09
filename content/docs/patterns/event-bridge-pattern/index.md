---
title: "Event Bridge"
tags: ["AL","Interface","Extendability"]
categories: ["Pattern"]
---

_Created by waldo & Arend-Jan Kauffmann, Described by waldo_

## Abstract

In the world of interfaces, it is important to preserve (certain) events over multiple implementation of the interface.

## Context

An app can have interfaces.
It makes it possible for other apps to extend/change the implementations of a certain part of the business logic.

Like in this example, we have an interface, to implement different ways for getting weights from scales:

```AL
interface "IScale"
{
    procedure GetWeight(): Decimal;
    procedure Tare();
}
```

## Problem

Multiple apps can subscribe to certain events of the app.
When a new implementation is created, we need to make sure that these events are raised at the right times.  If those events were published on the implementation codeunit, it might very well be that those events will not be raised, hard to find, or whatever.

So, if we would implement it like this, it isn't really extensible, as a different implemention would implement different events .. and it's not possible to subscribe to all of them (including future implementations)

```AL
codeunit 50407 "Scale Wrong" implements IScale
{
    procedure GetWeight() Result: Decimal;
    begin
        //TODO: Implement Bar GetWeight
        OnAfterGetWeight(Result);
    end;

    procedure Tare();
    begin
        //TODO: Implement Bar Tare
        OnAfterTare();
    end;

    [IntegrationEvent(false, false)]
    procedure OnAfterGetWeight(var Result: Decimal)
    begin
    end;

    [IntegrationEvent(false, false)]
    procedure OnAfterTare()
    begin
    end;
}
```

## Description

To mitigate this problem, we can work with a new, dedicated and isolated codeunit, with publisher events to be able to raise them from different places.

```AL
codeunit 50406 "IScale Triggers"
{
    [IntegrationEvent(false, false)]
    procedure OnAfterGetWeight(var Result: Decimal)
    begin
    end;

    [IntegrationEvent(false, false)]
    procedure OnAfterTare()
    begin
    end;
}
```

This way, it's possible to raise the events in all the implementations:

```AL
codeunit 50405 "Scale Bar" implements IScale
{
    var
        IScaleTriggers: Codeunit "IScale Triggers";

    procedure GetWeight() Result: Decimal;
    begin
        //TODO: Implement Bar GetWeight
        IScaleTriggers.OnAfterGetWeight(Result);
    end;

    procedure Tare();
    begin
        //TODO: Implement Bar Tare
        IScaleTriggers.OnAfterTare();
    end;
}
```

## Benefits

This new codeunit, with public events, makes the events accessible from all places, including new apps that are dependent from this app.

The naming convention (both starting with "IScale") also makes it very easy to find that corresponding events for the interface.

## When not to use

Obviously, the events should be carefully considered: only the events that make sense to "share" over all implementations, need this approach.
