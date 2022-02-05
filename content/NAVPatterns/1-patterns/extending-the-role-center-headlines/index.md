+++
title = "Extending the Role Center Headlines"
weight = 560
+++
_By David Bastide at Microsoft Development Center Copenhagen_

[![ ][image0]][anchor0]

## Context

  
Headlines are designed as a page of type HeadlinePart containing at least one text field. The part is added to the top of Role Center pages.   
This document provides an elegant and extensible pattern about how to extend the Role Center headlines to add your own business headlines based on your data, and display them only if relevant.

## Description

  
The Dynamics 365 Business Central release (April 2018) introduces a new HeadlinePart page type. This page type defines a page that rotates a display of several headlines after another, in the web client. A user can also click to switch to another headline. Headlines can also include a drilldown action that will be invoked when the user clicks the headline Text of the payload can be emphasized.  
Headlines are divided in 2 parts: the qualifier, and the payload as you can see in the figure below.  

[![ ][image1]][anchor1]

_Figure 1: Qualifier, Payload and emphasized text._

## Usage  

A page part has been added to each major Role Center:

* Page 9006 Order Processor Role Center, contains Page 1441 Headline RC Order Processor
* Page 9015 Job Project Manager RC, contains Page 1443 Headline RC Project Manager
* Page 9022 Business Manager Role Center, contains Page 1440 Headline RC Business Manager
* Page 9024 Security Admin Role Center, contains Page 1445 Headline RC Administrator
* Page 9026 Sales & Relationship Mgr. RC, contains Page 1444 Headline RC Relationship Mgt.
* Page 9027 Accountant Role Center, contains Page 1442 Headline RC Accountant
* Page 9028 Team Member Role Center, contains Page 1446 Headline RC Team Member
* Page 9010 Production Planner Role Center, contains Page 1447 Headline RC Prod. Planner
* Page 9016 Service Dispatcher Role Center, contains Page 1448 Headline RC Serv. Dispatcher

You can create extensions that extend these pages to add your own headlines.  
If no headline is added on these pages, fallback headlines will be displayed.  
  
The process to extend the headlines of a Role Center is simple:

1. In a V2 extension, extend the pages (PAG1440 to 1446) with one or more fields you want to add as headlines. The field and its visibility should be variables that are populated in OnAfterGetRecord.
2. Subscribe to the OnComputeHeadlines event from the codeunits associated with the page (same ID and name as the page). Here you can compute your headlines. You should store the result in a table in your extension, so you can quickly get the results in step 3\. The computation is done in a background task, not to decrease the performance of the role center pages. 
3. Subscribe to the OnIsAnyExtensionHeadlineVisible event from the page. This event is used to determine if any extension has visible headlines, and if so, hide the fallback headlines. You should set the ExtensionHeadlinesVisible variable to true if your extension has headlines to display at the time of the event. Otherwise, do nothing.
4. In the page, in the OnAfterGetRecord trigger, get the headline text and visibility values and copy them to your added fields.  

To format headlines, you should use Codeunit 1439 Headline Management functions:

* Truncate: to truncate a text gracefully when possible with "...". For example, HeadlineManagement.Truncate('the text',6) returns "the...".
* Emphasize: to emphasize part of the headline payload. Emphasized text is shown with a different style.
* GetHeadlineText: to build the headline text. You provide the headline qualifier and payload, and you get the headline in a format that will be interpreted and formatted correctly by the client. It returns false if the qualifier exceeds its maximum length (50 characters) or payload exceeds its maximum length (75 characters). In that case it will not return the headline. If the qualifier you specify is empty, the default text "HEADLINE" will be displayed in the qualifier area. The payload must not be empty.

### Examples:

#### 1. Extending the page with a new headline:
```al
group(LargestSale)  
{
    Visible = LargestSaleVisible;
    ShowCaption=false;
    Editable=false;
      
    field(LargestSaleText;LargestSaleText)
    {
        ApplicationArea = Basic, Suite;
        DrillDown=true;
        
        trigger OnDrillDown()
        var
            EssentialBusHeadlineMgt: Codeunit "Essential Bus. Headline Mgt.";
        begin
            EssentialBusHeadlineMgt.OnDrillDownLargestSale();
        end;
    }
}  
```

#### 2. Subscribing to the OnComputeHeadlines event, and computing headlines  
```al
[EventSubscriber(ObjectType::Codeunit, Codeunit::"Headline RC Business Manager", 'OnComputeHeadlines', '', true, true)]
    procedure OnComputeHeadlinesBusinessManager()
    begin
    // \[...\] compute headline, and init the EssentialBusinessHeadline record
    if not ShowHeadline then
        exit; // not enough data to compute headline

    if not HeadlineManagement.GetHeadlineText(
        'Insight from last week',
        StrSubstNo('The largest posted sales invoice was for %1',
            HeadlineManagement.Emphasize(Format(CustomerLedgerEntry.Amount, 0, TypeHelper.GetAmountFormatWithUserLocale('$'))))
        EssentialBusinessHeadline."Headline Text")
    then
        exit;

    EssentialBusinessHeadline.Validate("Headline Visible", true);
    EssentialBusinessHeadline.Modify();
end;
``` 

#### 3. Subscribing to the OnIsAnyExtensionHeadlineVisible event

```al
[EventSubscriber(ObjectType::Page, Page::"Headline RC Business Manager", 'OnIsAnyExtensionHeadlineVisible', '', true, true)]
procedure OnIsAnyExtensionHeadlineVisible(var ExtensionHeadlinesVisible: Boolean)
var
    EssentialBusinessHeadline: Record "Essential Business Headline";
    AtLeastOneHeadlineVisible: Boolean;
begin
    EssentialBusinessHeadline.SetRange("Headline Visible", true);
    EssentialBusinessHeadline.SetFilter("Headline Name",'%1|%2|%3|%4|%5',
        EssentialBusinessHeadline."Headline Name"::LargestOrder,
        EssentialBusinessHeadline."Headline Name"::LargestSale,
        EssentialBusinessHeadline."Headline Name"::BusiestResource,
        EssentialBusinessHeadline."Headline Name"::MostPopularItem,
        EssentialBusinessHeadline."Headline Name"::SalesIncrease,
        EssentialBusinessHeadline."Headline Name"::TopCustomer);

    AtLeastOneHeadlineVisible := not EssentialBusinessHeadline.IsEmpty();
    // only modify the var if this extension is making some headlines visible, setting to false could override some other extensions setting the value to true
    if AtLeastOneHeadlineVisible then
        ExtensionHeadlinesVisible := true;
end;
```
#### 

#### 4. Setting the headline text on the page 

```al
trigger OnAfterGetRecord()
begin
    EssentialBusinessHeadline.GetHeadline(EssentialBusinessHeadline."Headline Name"::LargestSale);
    LargestSaleVisible := EssentialBusinessHeadline."Headline Visible";
    LargestSaleText := EssentialBusinessHeadline."Headline Text";
end;
```

[![ ][image2]][anchor2]

_Figure 2: Sequence diagram of headline usage_

## Usages in NAV:

* Essential Business Headlines extension


[anchor0]: 3733.logo.png
[anchor1]: Headline.png
[anchor2]: 0724.Headline-sequence-diagram-v2.png


[image0]: 3733.logo.png
[image1]: Headline.png
[image2]: 0724.Headline-sequence-diagram-v2.png
