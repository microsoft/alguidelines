---
title: "IsTemporary record safeguard"
tags: ["AL"]
categories: ["Best Practice"]
---

_Created by Kine, Described by Kine_

## Description

When you are working with temporary tables or real tables, you can have code, where you assume that Record variable is or is not temporary. Best practice is to not assume, but test it to be sure. In history,
many developers went through painful period when they did unwanted "DeleteAll" over real table in production database, because they were only assuming something (mostly it happened only once to them).

Therefore it is good practice to use [Record.IsTemporary()](https://docs.microsoft.com/en-us/dynamics365/business-central/dev-itpro/developer/methods-auto/record/record-istemporary-method) method to test this predicate, mainly when you are doing destructive action.

Another case when it is good practice to use this test is when you are subscribing to triggers on table. In most cases, you do not want to run your code when the trigger is running over temporary record. And you cannot assume, that
this specific table will not be used as temporary by someone else. Reacting to the trigger as if it was triggered by real table change could lead to corrupted data or unpredictable errors and the reason could be hard to find.

## Bad code

```al
    ShouldBeTemporary.DeleteAll(true);
```

or

```al
    [EventSubscriber(ObjectType::Table, Database::"Some Table", 'OnAfterInsertEvent', '', false, false)]
    local procedure DoSomethingOnAfterInsertSomeTable(var Rec: Record "Some Table")
    begin
        DoSomething(Rec);
    end;
```

## Good code

```al
    if ShouldBeTemporary.IsTemporary() then
        ShouldBeTemporary.DeleteAll(true);
```

or

```al
    if not ShouldBeTemporary.IsTemporary() then
        Error(RecNotTemporaryErr);
    ShouldBeTemporary.DeleteAll(true);
```

or

```al
    [EventSubscriber(ObjectType::Table, Database::"Some Table", 'OnAfterInsertEvent', '', false, false)]
    local procedure DoSomethingOnAfterInsertSomeTable(var Rec: Record "Some Table")
    begin
        if Rec.IsTemporary() then
            Exit;
        DoSomething(Rec);
    end;
```
