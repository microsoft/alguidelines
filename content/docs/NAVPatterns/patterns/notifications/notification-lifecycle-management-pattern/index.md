+++
title = "Notification Lifecycle Management Pattern"
weight = 880
tags = ["C/AL"]
categories = ["Pattern"]
+++
_By David Bastide at Microsoft Development Center Copenhagen_

[![ ][image0]][anchor0]

  

**Context**

This pattern is about sending notifications in Dynamics NAV, starting with version 2018, tracking them in the Notification Lifecycle Management framework, and recalling them when needed. 

  

**Description**

Notifications are easy to use in a wide range of cases. Instead of using notifications in a fire-and-forget way, we need to track them so that we can recall them if we need to. 

If we can have only one notification on a given page, an easy and efficient solution is to use a predefined Notification ID, as suggested in the ["Using In-context Notifications"][anchor7] pattern. 

However, some cases can be more complicated. For example, when you are adding lines to a table, what if several lines raise individual notifications? Using the same notification ID for each notification will no longer work because the latest notification overwrites the previous ones. Only one notification for a given notification ID can exist, and only the notification message would be updated. This is illustrated in Figure 1\. 

[![ ][image1]][anchor1] 

_Figure 1: Notification that an item that is not in stock. The notification ID is a predefined GUID, 2712AD06-C48B-4C20-820E-347A60C9AD00, for example._

[![ ][image2]][anchor2]

_Figure 2: You add a second item that is not in stock. the notification is fired with the same GUID, 2712AD06-C48B-4C20-820E-347A60C9AD00, for example. The previous notification is overwritten._

Here is the code for this behavior: 

```al
LOCAL PROCEDURE CreateAndSendNotification@23(UnitOfMeasureCode@1010 : Code[20];InventoryQty@1009 : Decimal;GrossReq@1008 : Decimal;ReservedReq@1007 : Decimal;SchedRcpt@1006 : Decimal;ReservedRcpt@1005 : Decimal;CurrentQuantity@1004 : Decimal;CurrentReservedQty@1003 : Decimal;TotalQuantity@1002 : Decimal;EarliestAvailDate@1001 : Date) : Boolean; 
VAR 
  ItemAvailabilityCheck@1011 : Page 1872; 
  AvailabilityCheckNotification@1000 : Notification; 
BEGIN 
  AvailabilityCheckNotification.ID(GetItemAvailabilityNotificationId);
  AvailabilityCheckNotification.MESSAGE(STRSUBSTNO(NotificationMsg,ItemNo)); 
  AvailabilityCheckNotification.SCOPE(NOTIFICATIONSCOPE::LocalScope); 
  AvailabilityCheckNotification.ADDACTION(DetailsTxt,CODEUNIT::"Item-Check Avail.",'ShowNotificationDetails');

  ItemAvailabilityCheck.PopulateDataOnNotification(AvailabilityCheckNotification,ItemNo,UnitOfMeasureCode,InventoryQty,GrossReq,ReservedReq,SchedRcpt,ReservedRcpt,CurrentQuantity,CurrentReservedQty,TotalQuantity,EarliestAvailDate); 
  AvailabilityCheckNotification.SEND; 
  EXIT(FALSE); 
END;

LOCAL PROCEDURE **GetItemAvailabilityNotificationId**@27() : GUID; 
BEGIN 
  EXIT('2712AD06-C48B-4C20-820E-347A60C9AD00');
END; 
```

An easy fix would be to dynamically generate the notification ID. However, what if you fix the issue that triggered the notification? 

Here is the code for this possible fix: 

```al
LOCAL PROCEDURE CreateAndSendNotification@23(UnitOfMeasureCode@1010 : Code[20];InventoryQty@1009 : Decimal;GrossReq@1008 : Decimal;ReservedReq@1007 : Decimal;SchedRcpt@1006 : Decimal;ReservedRcpt@1005 : Decimal;CurrentQuantity@1004 : Decimal;CurrentReservedQty@1003 : Decimal;TotalQuantity@1002 : Decimal;EarliestAvailDate@1001 : Date) : Boolean; 
VAR 
  ItemAvailabilityCheck@1011 : Page 1872; 
  AvailabilityCheckNotification@1000 : Notification; 
BEGIN 
  AvailabilityCheckNotification.ID(CREATEGUID);
  AvailabilityCheckNotification.MESSAGE(STRSUBSTNO(NotificationMsg,ItemNo)); 
  AvailabilityCheckNotification.SCOPE(NOTIFICATIONSCOPE::LocalScope); 
  AvailabilityCheckNotification.ADDACTION(DetailsTxt,CODEUNIT::"Item-Check Avail.",'ShowNotificationDetails'); 

  ItemAvailabilityCheck.PopulateDataOnNotification(AvailabilityCheckNotification,ItemNo,UnitOfMeasureCode,InventoryQty,GrossReq,ReservedReq,SchedRcpt,ReservedRcpt,CurrentQuantity,CurrentReservedQty,TotalQuantity,EarliestAvailDate); 
  AvailabilityCheckNotification.SEND; 
  EXIT(FALSE); 
END; 
```

Now, notifications do not replace each other, but we cannot recall them because we do not track each notification ID. 

[![ ][image3]][anchor3] 

_Figure 3: Three sales lines with a notification for each one_

Imagine that you're adding several items to a sales order, and inventory is low for some of the items. Each sales line will send a notification for its item if the quantity to sell is higher than the available inventory. By using dynamically generated notification IDs (**CREATEGUID**), each notification will not be overwritten, which is what we want. This is shown in Figure 3\. But after you see the notification, you may decide to decrease the quantity in the sales line. At that point, the notification should be recalled. To do that, we need a way to track the notifications and their IDs. 

  

**Solution**

The solution is to use the Notification Lifecycle Management framework. 

The framework allows you to keep track of notifications by saving notification IDs and with other useful information (record ID and optional additional context) that will allow you to easily find the notification IDs to recall. This can be seen as an internal dictionary to put and get notification IDs. 

  

This framework has three main components: 

* A temporary, in-memory table: **Notification Context** (1519). This table stores each notification GUID, the record ID of the record that caused each notification (on what object), and optionally, an additional GUID that represents an additional context: the cause of the notification (why). This lets you track and recall each notification. You can fire-and-forget the notification, but if you need to recall it you can find it by using the record ID of the cause and the optional additional context ID. 
* A singleton codeunit: **Notification Lifecycle Mgt.** (1511), that provides functions to create new notification context lines and recall them. This codeunit contains one instance of the temporary table **Notification Context**. 

* A helper codeunit:**Notification Lifecycle Helper** (1508), that subscribes to various events and makes the calls to the **Notification Lifecycle Mgt.** codeunit. 

  

The unit tests for this framework are in codeunit **Notification Lifecycle Tests** (139480). 

  

The main functions provided by codeunit 1511 are: 

* **SendNotification**(NotificationToSend : Notification;RecId : RecordID) 
  * Sends a notification and keeps track of it in the simplest way. We have a notification to send and the record ID of the object that triggered the notification. 
* **SendNotificationWithAdditionalContext**(NotificationToSend : Notification;RecId : RecordID;AdditionalContextId : GUID) 
  * Sends a notification and keeps track of it with additional information. For example, a GUID that represents the context in which the notification was sent, and an item with insufficient inventory. 
* **RecallNotificationsForRecord**(RecId : RecordID;HandleDelayedInsert : Boolean) 

  * Recalls all notifications that were sent by a given record ID. The HandleDelayedInsert flag should be TRUE if it is possible that the record ID provided is from a record that was not yet in the database (TRUE unless we recall notifications after deletion of a record). 
* **RecallNotificationsForRecordWithAdditionalContext**(RecId : RecordID;AdditionalContextId : GUID;HandleDelayedInsert : Boolean) 
  * Recalls the notification that was sent by a given Record ID in a particular context. The HandleDelayedInsert flag should be TRUE if it is possible that the Record ID provided is from a record that was not yet in the database (TRUE unless we recall notifications after deleting a record). 
* **SetRecordID**(RecId : RecordID) 
  * Sets the record ID after delayed insertion of a record. This will update the initially incomplete Record ID in the **Notification Context** table to a full Record ID. 

* **UpdateRecordID**(CurrentRecId : RecordID;NewRecId : RecordID) 
  * Replace CurrentRecId with NewRecId in the **Notification Context** table. This is called by **SetRecordId**. 

  

**Usage**

  

The simple case is: 

1. We create a notification object.
2. We call **SendNotification** or **SendNotificationWithAdditionalContext**.
3. When we want to recall the notification, we call **RecallNotificationsForRecord** or **RecallNotificationsForRecordWithAdditionalContext**. 

[![ ][image4]][anchor4]

_Figure 4: without additional context_

[![ ][image5]][anchor5] 

_Figure 5: with additional context_

However, delayed insert means that the simple case seen above doesn't happen very often. The issue is that when we call **SendNotification**, we provide the cause object's record ID. If this object has not been inserted yet, which is often the case when the user creates a new invoice, a new line, and so on, the record ID is incomplete. When the object is inserted the record ID is completed, but if we call **RecallNotificationsForRecord** at a later point, the record ID will be different from the incomplete record ID we used when sending the notification. The solution is to detect that the object is not yet inserted when we send the notification, and at a later point, set the record ID when the cause object is inserted.  

  

The realistic case is: 

1. We have a temporary object with a partially complete ID. Something like: Sales Line, 1000, "". 
2. We send a notification caused by this object (item out of stock). 
3. The temporary table receives NotificationId, empty record Id (Quote, ""), additional context (item out of stock). 
4. When the user leaves the field, the line is inserted. We replace the empty record ID (Quote, "") by the full record ID (Sales Line, 1000, 10000).
5. We recall the notification (the user put a lower quantity for example). 
6. We search for records with the full record ID and the additional context (item out of stock). 
7. If found, they are recalled.   

[![ ][image6]][anchor6] 

_Figure 6: delayed insert, with additional context_

**Usages in NAV:**

COD311 (Item-Check Avail.) 

COD312 (Cust-Check Cr. Limit) 

COD1508 (Notification Lifecycle Handler) 

**Related Patterns:**

[In-context notifications][anchor7]

[Singleton codeunit][anchor8]


[anchor0]: 6138.logo.png
[anchor1]: 4807.1st-notification.PNG
[anchor2]: 8512.2nd-notification.PNG
[anchor3]: 0677.3-notifications-smaller.PNG
[anchor4]: sequence1.png
[anchor5]: sequence2.png
[anchor6]: sequence3.png
[anchor7]: /navpatterns/1-patterns/notifications/in-context-notifications/
[anchor8]: /navpatterns/1-patterns/singleton/singleton-codeunit/


[image0]: 6138.logo.png
[image1]: 4807.1st-notification.PNG
[image2]: 8512.2nd-notification.PNG
[image3]: 0677.3-notifications-smaller.PNG
[image4]: sequence1.png
[image5]: sequence2.png
[image6]: sequence3.png
