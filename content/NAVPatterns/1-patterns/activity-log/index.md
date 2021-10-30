+++
title = "Activity Logs"
weight = 210
+++
## _by Ciprian Iordache at Microsoft Development Center Copenhagen_

## Activity Log 

**Abstract**

The Activity Log pattern tracks execution of activities. This is a Dynamics NAV specific implementation of the [Audit Log][anchor0] pattern.

[![ ][image0]][anchor1]  

**Problem**

In general, integrating with external systems can be very challenging, due to the complexity of the situation -- connectivity issues, asynchronous operations, user errors, etc. These challenges require sometimes re-trying several times, polling the external system, re-send/re-get data as all these activities can succeed but can very well fail.

Similar challenges exist in situations where a lengthy, complex task, composed of different steps is to be executed by various people in various timeframes. In case of errors (but sometimes also in case of success) there will be a need to track these activities to see what happened and the actual person which did a specific step. 

In all these cases, we need to be able to troubleshoot.

A tracking/logging functionality could be implemented for each activity/step separately, but this would lead to code duplication and problems in maintaining the code in future.

In NAV there is already the Change Log functionality which can record all the data changes that have been done to specific tables, specific fields. However, this functionality is not available for activities. Also, there are few places where separate logging/tracking implementations were done but the current pattern proposes an unified, central way of data recording and enables the user to track all/most of the activities. 

**Solution**

The Activity Log pattern tracks specific outcome of the activities, in order to be able to assess what went wrong/fine or who performed a specific activity. 

Activity Log pattern

* records the activity and its outcome (error or success messages)
* assembles all messages in one central view and presents them to the user filtered for the specific activity and ordered in reverse chronological order.

Figure below illustrates the how the Activity Log manifests in the UI. The figure shows a part of an activity log for a posted document that was sent to the document exchange service and illustrates both successful and failed activities. 

[![ ][image1]][anchor2]  

This functionality is implemented in the following way - Activity Log table (TAB710) contains a simple function that allows you to log the result of a task or activity:

ActivityLog.LogActivity(ContextRecordID,ActivityLog.Status::Failed,ContextDescription,ActivityDescription,ActivityMessage);

Similar to TAB700 for Error Messaging, the Activity Log table contains a RECORDID that is a link to the parent/context entity. That permits the Activity Log to be used in a generic way, for any kind of entities (tables) and it also permits filtering the data to a specific related entity only before being presenting to the user.

The following parameters should be provided to the function:

* RecordID: The record/context for which the activity is logged
* Status: The task/activity outcome
* Descriptions/Messages: fields that will clearly describe the state and outcome of the task

To show the log, add a page action, with the caption including the name "<prefix\> Log" and link it to the image named "Log":

    { ;1 ;Action ;
    Name=ActivityLog;
    CaptionML=ENU=Activity Log;
    ToolTipML=ENU=View the status and any errors if the document was sent as an electronic document or OCR file through the document exchange service.;
    ApplicationArea=\#Basic,\#Suite;
    Image=Log;
    OnAction=VAR  
    ActivityLog@1000 : Record 710;   
    BEGIN
    ActivityLog.ShowEntries(RECORDID);
    END;
    }

**NAV usages**

In Dynamics NAV 2016, there is a new feature for sending documents in electronic format to a document exchange service. In this case, sending documents requires multiple steps as it is an asynchronous activity and as such, in order to keep track of what's happening and when the Activity Log functionality was used. That offers later the possibility to see who sent and when a document was sent, when it was dispatched, if any dispatch errors and how many tries have been made until the document was finally dispatched or rejected. 

So as usages in NAV 2016, we have the document exchange and OCR features plus the related posted documents involved in the document exchange feature.

* COD1294.TXT
* COD1410.TXT
* PAG1270.TXT
* PAG1275.TXT
* PAG143.TXT
* PAG144.TXT
* PAG189.TXT
* TAB112.TXT
* TAB114.TXT
* TAB130.TXT

**Ideas for improvement**

Replace the scattered similar functionality (as mentioned above, we have several places having close functionality or similar requirements) with this new pattern.

**Consequences**

* Use with caution, similar to the Change Log functionality, as if the pattern will be used extensively in all the activities/operations within NAV, the table might become large containing many records and might cause some performance issues when presenting the data to the client (filtering on the specific activity). 
* Do not log private or confidential information (passwords, amounts, salaries, sensitive data), unless you are ok with this data to be showed to all users (even to users which normally would not have access to this data), thus overriding the permission sets. 
* Log only essential information (quality over quantity). Can the logged data be used to analyze the problem, or is it just junk data?  

**NAV Versions**

Supported from NAV 2016

**Related Topics**

Error Message Processing -- provides a similar view and uses similar concepts: has a generic implementation (uses as link the same RECORDID feature) and uses same filtering functionality when displaying the data to the user. 

Audit Log -- as mentioned in the beginning, this pattern is a NAV specific implementation of the audit log pattern. 



[anchor0]: http://martinfowler.com/eaaDev/AuditLog.html
[anchor1]: Activity-Log.jpg
[anchor2]: Activity-Log-NAV.jpg


[image0]: Activity-Log.jpg
[image1]: Activity-Log-NAV.jpg
