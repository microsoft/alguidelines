+++
title = "Subscriber Codeunits"
weight = 1180
+++

<_Created by waldo, Described by waldo_\>

## Description

In general, subscribers have to be put in codeunits.  There are a few performance considerations that you should keep in the back of your minds, when designing such a codeunit.

- Keep the codeunit as small as possible
- Work with a single instance codeunit
- only subscribe when necessary
- Avoid generic OnInsert/OnModify/OnDelete

Let's discuss all points

## Keep the codeunit as small as possible
Every time a subscriber gets called, a new instance of the codeunit is being loaded in memory, which takes memory and processing power.  The smaller the codeunit, the less memory, and the faster it is.  

Therefore, it's suggested to split the subscribers by functionality and avoid putting business logic in the actual codeunit.  Tip: put all business logic in an "[Method Codeunit](https://alguidelines.dev/bcpatterns/generic-method-pattern/)".

Examples:
- if you app does things on Sales and Purchase, create a Sales-subs codeunit, and a Purchase-subs.
- if you have multiple functionalities in your app (let's call'm modules), create a subs-codeunit per module, and only add the subscribers in there that are necessary for that module.

### Bad code
```AL
codeunit 2037325 "Setup Subs"
{
    SingleInstance = true;

    [EventSubscriber(ObjectType::Codeunit, Codeunit::"Manual Setup", 'OnRegisterManualSetup', '', false, false)]
    local procedure OnRegisterManualSetup(sender: Codeunit "Manual Setup")
    var
        AppId: ModuleInfo;
        NameListLbl: Label 'Linked Texts Framework - List', Locked = true;
        DescriptionListLbl: Label 'Edit linked texts', Locked = true;
        KeyWordListLbl: Label 'LT,Distri,Technical,Functional,Reports', Locked = true;
        NameReportLbl: Label 'Linked Texts Framework - Reports', Locked = true;
        DescriptionReportLbl: Label 'View linked texts reports', Locked = true;
        KeyWordReportLbl: Label 'LT,Distri,Technical,Functional,Reports', Locked = true;
    begin
        navapp.GetCurrentModuleInfo(AppId);
        Sender.Insert(NameListLbl, DescriptionListLbl, KeyWordListLbl, page::"LTE Linked Text List", AppId.Id(), "Manual Setup Category"::General);
        Sender.Insert(NameReportLbl, DescriptionReportLbl, KeyWordReportLbl, page::"LTE Linked Texts Reports", AppId.Id(), "Manual Setup Category"::General);
    end;
        
    [EventSubscriber(ObjectType::Codeunit, codeunit::"Manual Setup", 'OnRegisterManualSetup', '', false, false)]
    local procedure OnRegisterManualSetup(sender: Codeunit "Manual Setup")
    var
        AppId: ModuleInfo;
        NameLayoutLbl: Label 'Report Helper - Layout', Locked = true;
        DescriptionLayoutLbl: Label 'Set up or update report layout list', Locked = true;
        KeyWordLayoutLbl: Label 'RH,Distri,Technical,Functional,Reports,Layout', Locked = true;
        NameCaptionsLbl: Label 'Report Helper - Captions', Locked = true;
        DescriptionCaptionsLbl: Label 'Set up or update captions list', Locked = true;
        KeyWordCaptionsLbl: Label 'RH,Distri,Technical,Functional,Reports,Captions', Locked = true;
        NameFunctionsLbl: Label 'Report Helper - Functions', Locked = true;
        DescriptionFunctionsLbl: Label 'Set up or disable functions', Locked = true;
        KeyWordFunctionsLbl: Label 'RH,Distri,Technical,Functional,Reports,Functions', Locked = true;
        NameDFCLbl: Label 'Report Helper - Default Footer', Locked = true;
        DescriptionDFCLbl: Label 'Set up or update default footer', Locked = true;
        KeyWordDFCLbl: Label 'RH,Distri,Technical,Functional,Reports,Default,Footer', Locked = true;
    begin
        navapp.GetCurrentModuleInfo(AppId);
        Sender.Insert(NameLayoutLbl, DescriptionLayoutLbl, KeyWordLayoutLbl, page::"RHE Report Layout List", AppId.Id(), "Manual Setup Category"::General);
        Sender.Insert(NameCaptionsLbl, DescriptionCaptionsLbl, KeyWordCaptionsLbl, page::"RHE Captions", AppId.Id(), "Manual Setup Category"::General);
        Sender.Insert(NameFunctionsLbl, DescriptionFunctionsLbl, KeyWordFunctionsLbl, page::"RHE Functions", AppId.Id(), "Manual Setup Category"::General);
        Sender.Insert(NameDFCLbl, DescriptionDFCLbl, KeyWordDFCLbl, page::"RHE Default Footer Card", AppId.Id(), "Manual Setup Category"::General);
    end;
}
```
### Good code

Split into 2 codeunits, and move the business logic out.

```AL
codeunit 2037325 "LTE Setup Subs"
{
    SingleInstance = true;

    [EventSubscriber(ObjectType::Codeunit, Codeunit::"Manual Setup", 'OnRegisterManualSetup', '', false, false)]
    local procedure OnRegisterManualSetup(sender: Codeunit "Manual Setup")
    var
        RegisterLTEManualSetup: codeunit "Register LTE Manual Setup";
    begin 
        RegisterLTEManualSetup.RegisterLTEManualSetup();
    end;
}

codeunit 2037324 "RHE Setup Subs"
{
    SingleInstance = true;

    [EventSubscriber(ObjectType::Codeunit, Codeunit::"Manual Setup", 'OnRegisterManualSetup', '', false, false)]
    local procedure OnRegisterManualSetup(sender: Codeunit "Manual Setup")
    var
        RegisterRHEManualSetup: codeunit "Register RHE Manual Setup";
    begin 
        RegisterRHEManualSetup.RegisterRHEManualSetup();
    end;
}


```

## Work with a single instance codeunit

To avoid the extra "loading of the content" while a subscriber is being executed, use Single Instance codeunit for subscribers.  Do take into account, of course, that it would share the state across the entire session.

### Bad code
```AL
codeunit 2037324 "RHE Setup Subs"
{
    [EventSubscriber(ObjectType::Codeunit, Codeunit::"Manual Setup", 'OnRegisterManualSetup', '', false, false)]
    local procedure OnRegisterManualSetup(sender: Codeunit "Manual Setup")
    var
        RegisterRHEManualSetup: codeunit "Register RHE Manual Setup";
    begin 
        RegisterRHEManualSetup.RegisterRHEManualSetup();
    end;
}
```
### Good code
```AL
codeunit 2037324 "RHE Setup Subs"
{
    SingleInstance = true;

    [EventSubscriber(ObjectType::Codeunit, Codeunit::"Manual Setup", 'OnRegisterManualSetup', '', false, false)]
    local procedure OnRegisterManualSetup(sender: Codeunit "Manual Setup")
    var
        RegisterRHEManualSetup: codeunit "Register RHE Manual Setup";
    begin 
        RegisterRHEManualSetup.RegisterRHEManualSetup();
    end;
}
```

## only subscribe when necessary

If possible, only execute the subscriber when really necessary by using Manual Binding.

### Bad code
```AL
    //subscriber - code should actually only run when Color=Red.
    [EventSubscriber(ObjectType::Table, Database::"Just Some Table WLD", 'OnAfterValidateEvent', 'Message 2', false, false)]
    local procedure JustDoSomthing(var Rec: Record "Just Some Table WLD"; var xRec: Record "Just Some Table WLD")
    begin
        if Rec.color <> 'RED' then
            exit; //only execute when necessary

        ...
    end;
    
    //business logic
    if JustSomeTable.FindSet() then
        repeat
            JustSomeTable.Validate("Message 2", format(Random(1000))); 
        until JustSomeTable.Next() < 1;
```
### Good code
```AL
    if JustSomeTable.FindSet() then
        repeat
            if JustSomeTable.Color = 'RED' then 
                BindSubscription(DemoSubs);

            JustSomeTable.Validate("Message 2", format(Random(1000)));

            if JustSomeTable.Color = 'RED' then 
                UnbindSubscription(DemoSubs);
        until JustSomeTable.Next() < 1;
```

## Avoid OnInsert/OnModify/OnDelete
The reason for this is, that it breaks the batch-calls:
- Any "OnInsert" subscriber breaks the bulk inserts, simply because it needs to perform an operation after every record that was inserted
- Any "OnModify" subscriber slows down the "ModifyAll", simply because it needs to perform an operation after every record that was modified.  I fact: 1 SQL call is turned into a loop of SQL calls.
- Any "OnDelete" subscriber slows down the "DeleteAll", simply because it needs to perform an operation after every record that was deleted.  I fact: 1 SQL call is turned into a loop of SQL calls.

Avoid subscribers to these events.

## [Discussions](https://github.com/microsoft/alguidelines/discussions/92)

You can discuss this guidelines [here](https://github.com/microsoft/alguidelines/discussions/92).

You can find discussions on all "Best Practices" [here](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices).

If you don't find the discussion of this guideline, please feel free to create a new one with the same title as this article.  

## References

The [Generic Method Pattern](https://alguidelines.dev/bcpatterns/generic-method-pattern/)