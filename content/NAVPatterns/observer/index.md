+++
title = "Observer"
weight = 900
+++
#### _By Nikolai L'Estrange, from TVision Technology Ltd. in the UK__  
_

### Abstract

Track all record changes against a defined table or set of tables.

### Problem

Microsoft Dynamics NAV has the built in ability to track all record changes against any table, however it does not always fire the triggers for every table.

### Solution

Create a setup table to define which other tables you want to track changes for, and optionally what triggers you want to fire, then link this up to the standard triggers in Codeunit 1\.

Codeunit 1 Application Management contains the triggers OnDatabaseInsert, OnDatabaseModify, OnDatabaseDelete and OnDatabaseRename which are what we need to subscribe to in order to track record changes. However these triggers are only fired sometimes. This is determined by the parameters set in the function GetTableTriggerSetup, which is called once per table per session.

In order to define which tables we are interested in we can create a new table with the following fields:

    **Observable Table:**
    "Table ID" Integer Object.ID WHERE (Type=CONST(Table))
    TrackInsert Boolean \[optional\]
    TrackModify Boolean \[optional\]
    TrackDelete Boolean \[optional\]
    TrackRename Boolean \[optional\]

Then we can create a Codeunit that will set the Table Trigger Setup parameters and also subscribe to the OnDatabase triggers.

    **LOCAL \[EventSubscriber\] GetTableTriggerSetup(TableId : Integer;VAR OnDatabaseInsert : Boolean;VAR OnDatabaseModify : Boolean;VAR OnDatabaseDelete : Boolean;VAR OnDatabaseRename : Boolean)**  
    IF Observable.GET(TableId) THEN BEGIN  
    IF Observable.TrackInsert THEN  
    OnDatabaseInsert := TRUE;  
    IF Observable.TrackModify THEN  
    OnDatabaseModify := TRUE;  
    IF Observable.TrackDelete THEN  
    OnDatabaseDelete := TRUE;  
    IF Observable.TrackRename THEN  
    OnDatabaseRename := TRUE;  
    END;  
    **LOCAL \[EventSubscriber\] OnDatabaseInsert(RecRef : RecordRef)**
    IF Observable.Get(RecRef.NUMBER) AND Observable.TrackInsert THEN
    //do something
    **LOCAL \[EventSubscriber\] OnDatabaseModify(RecRef : RecordRef)**
    IF Observable.Get(RecRef.NUMBER) AND Observable.TrackModify THEN
    //do something
    **LOCAL \[EventSubscriber\] OnDatabaseDelete(RecRef : RecordRef)**
    IF Observable.Get(RecRef.NUMBER) AND Observable.TrackDelete THEN
    //do something
    **LOCAL \[EventSubscriber\] OnDatabaseRename(RecRef : RecordRef;xRecRef : RecordRef)**
    IF Observable.Get(RecRef.NUMBER) AND Observable.TrackRename THEN
    //do something
    

_**Note:**_ In NAV2016 all these functions can be EventSubscribers that subscribe to the functions in Codeunit 1 as per above, in earlier versions of NAV these functions will need to be Global and called explicitly from within the Codeunit 1 functions.

### NAV Usages

Variations of this pattern exists in the standard product in:

* Codeunit 423 Change Log Management
* Codeunit 5150 Integration Management. In this Codeunit the tables that fire triggers are hardcoded in C/AL.

### Consequences

It is important that in our GetTableTriggerSetup function we only ever set the parameters to **TRUE**, and **never** set them to **FALSE**. This is because there may be other Codeunits listening to the triggers for that table, e.g. Change Log. This is also why we check the setup again within each trigger.

### Related Topics

This pattern was originally described in the following blog:

[https://geeknikolai.wordpress.com/2015/10/30/observer-pattern-in-dynamics-nav-2016/][anchor0]

### NAV Versions

* From NAV 2016 use the code as shown
* For earlier versions see _Note_ above



[anchor0]: https://geeknikolai.wordpress.com/2015/10/30/observer-pattern-in-dynamics-nav-2016/
