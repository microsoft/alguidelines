+++
title = "Cue Table"
weight = 440
+++
## Cue Table

_By Bogdana Botez at Microsoft Development Center Copenhagen  
_

_[![ ][image0]][anchor0]  
_

Cues are the second usual application of the [**Singleton Table**][anchor1] pattern in Dynamics NAV, after [**Setup Tables**][anchor2].

**Context**: The user gets overview information about the business on the Dynamics NAV Role Center page.

Figure 1 - Cue information in Dynamics NAV shows cue information seen by the user on the **Sales Order Processor** role center.

[![ ][image1]][anchor3]

The overview information consists of summed-up numbers, calculated from business data, like for example how many sales orders are still open, how many shipments are ready to go, or partially shipped, how many documents are waiting for approval etc.

**Problem**: NAV stores data in tables. By definition, a table is a repetitive structure containing multiple lines, each line having a different piece of the information. But sometimes this repetitive information needs to be summed-up or otherwise synthetized, and presented as an overview.

**  
**

**Solution:** Store overview information in a singleton table.

****

There are two ways of calculating overview information in NAV.

1. By using a FlowField. This applies for simpler calculations, like filtered or unfiltered counts, sums etc.
2. By writing C/AL code to perform custom calculations. Use this when:

*   * The way to calculate the overview is too complex for flow fields, or
  * The data needs to be pulled from an external system (like Dynamics CRM, QuickBooks or any external integration).

**  
**

The implementation of Cues is already described in detail on MSDN, in [Creating and Customizing Cues][anchor4] and in [Walkthrough: Creating a Cue Based on a FlowField][anchor5].

**NAV Usages**

Table 1 - Cue tables in Dynamics NAV****shows some examples of singleton tables used for creating Cues.

Table ID

Table Name

1313

Activities Cue

5370

CRM Synch. Job Status Cue

9042

Team Member Cue

9050

Warehouse Basic Cue

9051

Warehouse WMS Cue

9052

Service Cue

9053

Sales Cue

9054

Finance Cue

9055

Purchase Cue

9056

Manufacturing Cue

9057

Job Cue

9058

Warehouse Worker WMS Cue

9059

Administration Cue

9060

SB Owner Cue

9061

RapidStart Services Cue

9063

Relationship Mgmt. Cue

9069

O365 Sales Cue

9070

Accounting Services Cue

Table 1 - Cue tables in Dynamics NAV

___  
_



[anchor0]: Cue-Table.png
[anchor1]: /nav/w/designpatterns/151.singleton-table
[anchor2]: /nav/w/designpatterns/76.setup-table
[anchor3]: Cue-Table-Figure-1.JPG
[anchor4]: https://msdn.microsoft.com/en-us/library/dn789553(v=nav.90).aspx
[anchor5]: https://msdn.microsoft.com/en-us/library/ff477101(v=nav.90).aspx


[image0]: Cue-Table.png
[image1]: Cue-Table-Figure-1.JPG
