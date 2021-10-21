+++
title = "standard-journal.md"
weight = 1150
+++
_By Bogdana Botez, at Microsoft Development Center Copenhagen_

Meet the **Standard Journal** pattern, which gives the NAV user the advantage of storing transaction details and reusing them multiple times at later dates. For example, this is how to pay a monthly bill.

## Abstract

If the journal data can be reused later, the user has the possibility to save the current temporary transaction details. One example can be the case of monthly electricity payments. The user will manually enter the details for the first payment, of the current month. Next month, a part of the data will be the same, such as the vendor and transaction details. If the user has saved the initial monthly payment as a standard journal, then they can now reuse it to create the draft of the next monthly payment. Once the draft journal lines are created, they can be updated with the current month information.

## Description

When a journal is created, the user can invoke the Save as Standard Journal action to save the current journal for later use. When saving the journal as a standard journal, the user is required to choose a code, which is later used to identify the saved journal. The journal lines are stored in a separate table. There can be one standard journal saved per journal type and code.

Later, the user can create new journal lines by using the Get Standard Journals action. This action restores the saved journal into the new journal lines.

### Scenario

**Step 1**: The data entered by the user through the Journal page is stored temporarily in the Journal Line table. The data is available for editing or deleting. The journal line data will be stored in this table until it is either deleted or posted.

**Step 2**: The user decides to save the current journal line entries for later use. If this is the monthly rent, the user may want to use similar entries next month when a new payment is due. On the Journal page, the user invokes the Save as Standard Journal action. This triggers the Save as Standard Journal report, which copies the entries from the Journal Line table to the Standard Journal Line table. When saving, the user will be asked for an identifier, a code, which will be used to later uniquely identify the saved entries.

**Step 3**: When the user invokes the Get Standard Journal action, a list of codes are presented to the user so that they can decide which standard journal to restore and copy in the Journal Line table.

[][anchor0][![ ][image0]][anchor1] 

[![ ][image1]][anchor2][][anchor3]

The sequence flow of the three steps is described in the following diagram.

[![ ][image2]][anchor4][][anchor5][][anchor6]

## NAV Specific Example

In the standard version of NAV, the Standard Journal functionality is implemented in the following journals:

* Item Journal, which saves data to the Standard Item Journal
* General Journal, which saves data to the Standard General Journal

### General Journal

The user enters data in the General Journal page (39) and invokes the Save/Get actions as illustrated in the following screenshot:

[][anchor7][![ ][image3]][anchor8] 

When saving the journal lines, the Save as Standard Gen. Journal report (750) is invoked. The report saves the entries in the Standard General Journal Line table (751).

## NAV Usages

1. Standard General Journal
2. Standard Item Journal

[watch?v=XeTKmO2Eqgw&list=PLhZ3P LY7CqmVszuvtJLujFyHpsVN0Uw&index=21][anchor9]



[anchor0]: 3201.NAVPatternStdJournal1.png
[anchor1]: 0820.Standard-Document-Pattern-1.png
[anchor2]: 0143.Standard-Document-Pattern-2.png
[anchor3]: 6521.NAVPatternStdJournal2.png
[anchor4]: 5327.Standard-Document-Pattern-3.png
[anchor5]: 3618.Standard-Document-Pattern-3.png
[anchor6]: 1104.NAVPatternStdJournal3.png
[anchor7]: 8585.NAVPatternStdJournal3.jpg
[anchor8]: 0456.Standard-Journal.png
[anchor9]: https://www.youtube.com/watch?v=XeTKmO2Eqgw&list=PLhZ3P-LY7CqmVszuvtJLujFyHpsVN0U_w&index=21


[image0]: 0820.Standard-Document-Pattern-1.png
[image1]: 0143.Standard-Document-Pattern-2.png
[image2]: 5327.Standard-Document-Pattern-3.png
[image3]: 0456.Standard-Journal.png
