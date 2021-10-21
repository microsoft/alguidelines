+++
title = "read-once-initialization-and-validation.md"
weight = 970
+++
_By Tim Grant**  
**_

## **Abstract**

One time query and validation of a setup table. 

****

## **Description**

Firstly identifies if a setup table record is in memory, removing the need to execute and validate the re-query again within a code unit. (From NAV 2013+ service tier database caching exists).

If you need to query a setup table, but not sure if the table is yet in memory from earlier code, this helps to determine this call (see Singleton Table pattern).   

Allows a central function to validate critical fields before processing automated code. **TESTFIELD** calls are added to the function and not the INIT call in order to ensure that critical functions do not run without this human check in place as these may rely on key configuration. If you have a design where specific field values are critical (custom or standard) for integrity, then INIT INSERT is a dangerous default which could result in the corruption of processes. This is separate automated INIT INSERT from a setup page.

## **NAV Specific Example**

A global variable record and a global status indicator, but within the context of a local function.

[![ ][image0]][anchor0]

[![ ][image1]][anchor1]

![ ][image2]

## **NAV Usages**

A similar example of this is in Codeunit 80, but without any validation. 

[![ ][image3]][anchor2]

E.g. If there is a bespoke change, then this existing **GetGLSetup** can be called earlier to accommodate for an earlier bespoke change:

[][anchor3][![ ][image4]][anchor4]

This means that by the time the code reaches the original **GetGLSetup** code requirement, this record is already in memory as the status flag is set to True, eliminating another check. For such Patterns the setup record should never be cleared, it should be called once globally, initiated and left in memory for future checks. It should **not** be declared locally at the same time, review your code for matching local variable usage before implementing this.**** 

****

## **Ideas for improvement**

Merge **Singleton Table** with dynamic validation field meta configuration. This removes the coding of **TESTFIELD** to a central setup check.

Example with the **Sales & Receivables Setup****  
**

[![ ][image5]][anchor5]

[![ ][image6]][anchor6]

[![ ][image7]][anchor7]

****

## **Consequences**

If in the circumstance that a field value in the setup table is expected to change within the life of the code component instance. e.g. If a CHANGECOMPANY is required to cross reference another Setup table, use a separate variable.

## **Related Topics**

This is related to the **Singleton Table** pattern.



[anchor0]: 6332.1.png
[anchor1]: 5025.2.png
[anchor2]: 4.png
[anchor3]: 5.png
[anchor4]: 2465.5.png
[anchor5]: 5857.1.png
[anchor6]: 4477.2.png
[anchor7]: 4314.3.png


[image0]: 6332.1.png
[image1]: 5025.2.png
[image2]: 5672.8.png
[image3]: 4.png
[image4]: 2465.5.png
[image5]: 5857.1.png
[image6]: 4477.2.png
[image7]: 4314.3.png
