+++
title = "Cross Session Events"
weight = 430
+++

_By Nikolai L'Estrange, from TVision Technology Ltd. in the UK_  

## Abstract

Track things that happen in other NAV Sessions.

[![ ][image0]][anchor0]

## Problem

In Microsoft Dynamics NAV you can fire a function whenever something changes within your session (and from NAV 2016 this is even easier with the new Event model), however there is not an easy way to know what is happening in other sessions. Sometimes you would like to know what has happened since your last read, without reading everything again, e.g. when you need to pass a large dataset to a Control Add-in.

A common way of handling this with Ledger Tables is to make note of the last record you read, and continuously poll to see if there are any new records. However this is restricted to strictly sequentially entered tables.

## Solution

There is a common pattern in many other languages called [Publish-Subscribe][anchor1] (or PubSub) that solves the same issue. We can implement the same pattern in NAV using a Table as a message queue platform and polling this table. We have named this pattern "Cross Session Events" in order to avoid confusion with the standard NAV Events which use the terms Publisher and Subscriber, and to try and describe more accurately when you would need this pattern.

The pattern has four components:

* **Publisher(s)**: These push messages to the Message Broker.
* **Subscriber Records**: Identifies the Subscriber and store filters to say what messages the Subscriber and interested in receiving.
* **Message Broker**: This distributes all messages sent in to all Subscribers that have expressed an interest (i.e. the message is within their filters). 
* **Message Queue**: To hold the messages for each Subscriber. Generally once these messages are read, they are deleted.

## Example

An example of this would be when we have multiple users looking at the same set of data and we want their screens to update in "real time" whenever one of them makes a change, without doing a full refresh. We will use the [Observer pattern][anchor2] to capture the change (act as the Publisher) and then create a Table to hold Subscribers and Filters (Change Observer), a Table to be the Message Queue (Change Notification), and a Codeunit to be the Message Broker and help with the polling (ObserverMgt).

Below are the table definitions:
```al
    //Change Observer:   
    //"Table ID" Integer "Observable Table"  
    "Server ID" Integer  
    "Session ID" Integer   
      
    //Change Notification:  
    //"Table ID" Integer "Observable Table"  
    "Server ID" Integer  
    "Session ID" Integer   
    "Entry No." Integer AutoIncrement  
    "Type of Change" Option Insert,Modify,Delete,Rename  
    "Record ID" RecordID  
    ... (other fields to indicate what has changed)  
```
    

The Change Observer table identifies the Subscriber using Server ID and Session ID, and then in this example there is only one filter, which is the Table ID we want to listen to any changes. In this case all three fields are in the Primary Key.

The Change Notification table then has the same three fields plus an Entry No. as its Primary Key, and in this example borrows heavily from the Change Log code to fill in the rest of the message.

_**Note:**_ Other examples of the pattern could have very different fields to identify the Subscriber, Filters and then whatever fields needed for content of the Message.

Our Message Broker Codeunit will also serve as a central place to create Subscribers (Listen and StopListening functions) and a place to Poll for Messages. Note that the Poll function deletes the Messages as it reads them.
```al
    //Listen(TableID : Integer)**
    WITH Observer DO BEGIN
        "Table ID" := TableID;
        "Server ID" := SERVICEINSTANCEID;
        "Session ID" := SESSIONID;
        INSERT(TRUE);
        COMMIT;
    END;

    //StopListening(TableID : Integer)
    WITH Observer DO BEGIN
        RESET;
        SETRANGE("Server ID",SERVICEINSTANCEID);
        SETRANGE("Session ID",SESSIONID);
        SETRANGE("Table ID",TableID);
        DELETEALL(TRUE);
        COMMIT;
    END;

    //NotifyAll(ChangeNotification : Record "Change Notification")**
    WITH Observer DO BEGIN
        RESET;
        SETRANGE("Table ID",ChangeNotification."Table ID");
        IF FINDSET THEN REPEAT
        Notify(Observer,ChangeNotification);
        UNTIL NEXT = 0; 
    END;

    //Notify(Observer : Record "Change Observer";ChangeNotification : Record "Change Notification")**
    WITH ChangeNotification DO BEGIN
        "Server ID" := Observer."Server ID";
        "Session ID" := Observer."Session ID";
        "Entry No." := 0;
        INSERT;
    END;

    //Poll(TableID : Integer;VAR TempChangeNotification : TEMPORARY Record "Change Notification")**
    WITH ChangeNotification DO BEGIN
        TempChangeNotification.RESET;
        TempChangeNotification.DELETEALL;
        RESET;
        SETRANGE("Table ID",TableID);
        SETRANGE("Server ID",SERVICEINSTANCEID);
        SETRANGE("Session ID",SESSIONID);
        IF FINDSET THEN REPEAT
            TempChangeNotification := ChangeNotification;
            TempChangeNotification.INSERT;
            MARK(TRUE);
            UNTIL NEXT = 0;
        MARKEDONLY(TRUE);
        DELETEALL;
    END;
```

**__** The final part of this example is an object that calls the functions above. In this example we will use a Page with a PingPong Timer Control to do the polling in (almost) real time. These are the functions on the page:

```al
    //OnQueryClosePage(CloseAction : Action None) : Boolean**
    ObserverMgt.StopListening(DATABASE::"NAV Whiteboard Booking");
    
    //Timer::AddInReady()**
    IF ObserverMgt.Listen(DATABASE::"NAV Whiteboard Booking") THEN
    CurrPage.Timer.Ping(1000);
    
    //Timer::Pong()**
    CallUpdate;
    CurrPage.Timer.Ping(1000);
    
    //LOCAL CallUpdate()**
    ObserverMgt.Poll(DATABASE::"NAV Whiteboard Booking",TempChangeNotification);
    WITH TempChangeNotification DO BEGIN
        IF FINDSET THEN REPEAT
        IF "Type of Change" = "Type of Change"::Delete THEN BEGIN
        ...
        END ELSE IF RecRef.GET("Record ID") THEN BEGIN
        ...
        END;
        UNTIL NEXT = 0;
    END;
```

## Consequences

The PingPong control is only available on the Windows Client, so if you want to use another client you will need to use another solution to Poll for Messages. Therefore this pattern is not always going to be "real time".

## Related Topics

This pattern was originally described in the following blog:

[https://geeknikolai.wordpress.com/2015/10/30/pubsub-pattern-in-dynamics-nav-2016/][anchor3]

Below is the Wikipedia link to the PubSub pattern

[https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe\_pattern][anchor1]



[anchor0]: PubSub.png
[anchor1]: https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern
[anchor2]: /nav/w/designpatterns/248.observer
[anchor3]: https://geeknikolai.wordpress.com/2015/10/30/pubsub-pattern-in-dynamics-nav-2016/


[image0]: PubSub.png
