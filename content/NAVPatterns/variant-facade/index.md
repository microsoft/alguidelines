+++
title = "variant-facade.md"
weight = 1440
+++
# Variant Façade

By Nikola Kukrika, Waldo and Gary Winter

# Abstract

The Variant façade provides a single interface that can take any Record, RecordRef or RecordID as an argument. With this pattern the code is encapsulated within the single object, with clear separation between common and table specific code.

[![ ][image0]][anchor0]

****

# Problem

Since NAV is strongly typed, developers often need to duplicate functionality in order to add support for a new table. The developer would typically start by duplicating the function, changing the record type and implement slight modifications to the code if needed. 

The problem with this approach is that code duplication is one of the worst things in software development -- it makes code harder to understand, maintain, extend and test.

One of the good examples of this approach and the resulting duplication is codeunit 229, Document-Print:

[![ ][image1]][anchor1]

[![ ][image2]][anchor2]

After reading the code from the two code snippets above, it is very hard to see the differences. The most of the code is duplicated (more than 95%). To make the matters worse the codeunit itself has 10 additional methods that are used to print different records, some are similar to the ones above while others are completely different.

[![ ][image3]][anchor3]

[![ ][image4]][anchor4]

Figure: the entire code of the codeunit 229, Document Print, 12 methods

[![ ][image5]][anchor5]

Figure: Visualization how it would look like with 20 methods

Problems arising from this way of implementing code are:

**Understanding of the code** is very hard, since the developer needs to read blocks of code that look similar and try to understand what exactly the differences are.

**Maintaining and Extending** the code is hard, since every fix or a new behavior that needs to be implemented multiple times (in this case probably 12 times).

**Upgradability is low** -- each conflict needs to be resolved many times. The hook pattern is hard to implement since it needs to be implemented for every function. Evening suffers the same problem - cannot use the evening easily since it needs to be raised from multiple places in the code.

**Testing the code** is hard since the tests need to be replicated.

**Constant cost of adding support for new tables **-- when the new record type needs to be supported, it has a constant cost. Adding a support for a new record will increase the Maintenance tax and it will make the code harder to understand and extend. ****

**Conclusion **- **If the functionality needs to be used for many records the approach of duplicating the functions should be avoided.**

# Solution

The Variant façade pattern provides a single interface that would not need to be changed in the future. It will be able to take any record as a parameter. Common code should be kept separately from record specific code and both must be very visible so the developers can easily see what the differences are.

The Key components of the pattern are:

## Signature

Instead of hardcoding a record type a variant is used as an argument. A Variant Façade function can receive three types of data: Record, RecordID, or RecordRef. This way, it can be reused anywhere in the product and the code will still work.

A good practice is to combine this pattern with the Argument Table pattern to make sure that the signature does not change (Additional parameters can be added to the argument table without impacting existing code.). If the Argument Table is not needed, it should not be placed in the signature - it is always possible to add it at a latter point and have two public functions (one with and one without arguments).

Example - For codeunit 229, Document-Print all of the public methods can be simply replaced with a single public method like this:

With this approach the façade function can serve all of the record types and will not need to change in the future.

## Casting to a record ref

After the signature it is necessary to decide if the function will support passing of the Record ID and the RecordRef. Code tends to be easier to understand and maintain if only the records are passed as the arguments, however in some cases it is needed to support the other two types.

* **Support for Record, Record ID and RecordRef:**

After the signature if the function supports passing of the RecordID or RecordRef as a parameter it is a good practice to cast them to a RecordRef. Codeunit Data Type Management is used to do this:

DataTypeManagement.GetRecordRef(RecRelatedVariant,RecordRef)

* **Support Records only** - If the function supports only passing in the record, it is a good practice to check if the variant is a record:

IF NOT RecordVariant.ISRECORD THEN

ERROR(NotARecordErr);

## Using the variant

The variant can be passed instead of record when calling the Page.RUN, Codeunit.RUN or Report.RUN statically:

For example:

Page.RUN(PageID,Variant);

Codeunit.RUN(CodeunitID,Variant);

Report.RUN(ReportID,Variant);

These calls are identical to using an actual instance of the record, since the variant will be casted to the record automatically, with all filters, markings and values preserved.

****

In case the variant was casted to the RecordRef (by using DataTypeManagement.GetRecordRef(RecRelatedVariant,RecordRef)), it is still possible to invoke the functions statically.

The RecordRef simply needs to be casted into a variant and passed as a parameter, for example:

VariantArgument := RecordRef;

Page.RUN(PageID,VariantArgument);

Variant can always casted back to the original record in the table specific code, exact process is described below.

## Table specific code

To do table-specific processing, it is necessary to get the RecordRef first, since NUMBER parameter will tell us which table it is. In the table-specific code, it is possible to cast the variant back to the original record type, so data is accessible and it is possible to invoke functions.

To do this, the best practice is to use the COPY function to preserve filters:

SalesHeader.COPY(RecordVariant);

Assigning directly such as SalesHeader := RecordVariant, is possible, however all filters will be lost.

Example of table specific code:

CASE RecordRef.NUMBER OF

DATABASE::"Sales Header":

BEGIN

SalesHeader.COPY(RecordVariant);

SalesHeader.PrintDocument;

END;

DATABASE::"Purchase Header":

BEGIN

PurchaseHeader.COPY(RecordVariant)

...

END;

....

It is a good practice to try to avoid the table specific code if possible.

One of the issues with table specific code is that the CASE statement can easily explode when large number of records are supported.

There are two possible solutions:

1. Move the calculations outside of the façade code unit and pass it in as part of the argument table.

For example, instead of having a case like this within DocumentPrint codeunit:

CASE RecordRef.NUMBER OF

DATABASE::"Sales Header":

BEGIN

SalesHeader.COPY(RecordVariant);

CASE SalesHeader."Document Type" OF

SalesHeader."Document Type"::Quote:

ReportSelections.SETRANGE(Usage,ReportSelections.Usage::"S.Quote");

SalesHeader."Document Type"::"Blanket Order":

ReportSelections.SETRANGE(Usage,ReportSelections.Usage::"S.Blanket");

SalesHeader."Document Type"::Order:

ReportSelections.SETRANGE(Usage,ReportSelections.Usage::"S.Order");

SalesHeader."Document Type"::"Return Order":

ReportSelections.SETRANGE(Usage,ReportSelections.Usage::"S.Return");

SalesHeader."Document Type"::Invoice:

ReportSelections.SETRANGE(Usage,ReportSelections.Usage::"S.Invoice");

SalesHeader."Document Type"::"Credit Memo":

ReportSelections.SETRANGE(Usage,ReportSelections.Usage::"S.Cr.Memo");

END;

If the ReportSelections.Usage is simply passed into the function from outside, then the case statement is not needed at all. For example:

PrintDocumentArguments."Report Selection Usage" := SalesHeader.GetReportSelectionUsage;

DocumentPrint.PrintDocument(SalesHeader,PrintDocumentArguments);

Where GetReportSelection usage is coded like this:

CASE SalesHeader."Document Type" OF

SalesHeader."Document Type"::Quote:

EXIT (ReportSelections.Usage::"S.Quote");

SalesHeader."Document Type"::"Blanket Order":

EXIT(ReportSelections.Usage::"S.Blanket");

SalesHeader."Document Type"::Order:

EXIT(ReportSelections.Usage::"S.Order");

SalesHeader."Document Type"::"Return Order":

EXIT(ReportSelections.Usage::"S.Return");

SalesHeader."Document Type"::Invoice:

EXIT(ReportSelections.Usage::"S.Invoice");

SalesHeader."Document Type"::"Credit Memo":

EXIT(ReportSelections.Usage::"S.Cr.Memo");

This way the code is much more reusable and simpler to read.

1. Use Rules Table to replace the code with data-driven approach.

Setup table would contain the list of the reports and their usages. Based on Table ID and usage it is possible to set filters on the setup table and run the object ID from the result.

For example:

ReportSelectionSetup.SETRANGE("Table ID", RecordRef.NUBMER);

ReportSelectionSetup.SETRANGE("Usage Type", RecordRef.FieldValue(ArgumentTable."Usage Type");

ReportSelectionSetup.FINDFIRST;

REPORT.RUN(ReportSelectionSetup."Report ID",VariantRecord);

****

# Example

The following code illustrates how the Variant Façade pattern can be used to implement the Document-Print Codeunit.

[![ ][image6]][anchor6]

[![ ][image7]][anchor7]

From the PrintDocument signature it is clear that it will not need to be changed in the future.

Code duplication is avoided, specific code is isolated in the PrepareRecord function, there is clear extension point to add support for new records in the future. Since code is not duplicated and there is a single flow through the method, inserting hooks and events in the future will be straightforward.

When adding the support for new records in most cases it is not need to change any code within the method, thus the cost of extending the usage is minimal.

Note - PrepareRecord function is placed for the illustrational purposes. An improvement would be to move all of the code from the PrepareRecord function before calling the function. So for the SalesHeader and PurchaseHeaders discounts should be calculated before invoking the function. For passing of the argument it should be one of the fields in the Argument Table, thus the entire specific code would be eliminated.

# Consequences 

* Not needed if the functionality needs to support few tables. Don't use it as a hammer 
* Strongly typing the records has it benefits since it is easier to find usages, errors will be visible at the compilation time.
* Code becomes harder to debug within the variant façade
* Be careful with filters and marks, if the function needs to support multiple records. Test these cases thoughtfully because with bad placement of code the filters can easily be lost.
* Case statements can explode if there are too many tables that require specific processing. Then it is a must to find a way to keep the number of options in the CASE statement low. The Rules Table pattern and adding specifics to the Argument Table before invoking the code could help with keep the list shorter.

# NAV usages

* Codeunit 452 - Report Distribution Management
* Codeunit 700 - Page Management
* Codeunit 701 - Data Type Management
* Codeunit 1268 - Export Launcher
* Codeunit 1410 - Doc. Exch. Service Mgt.
* Codeunit 1501 - Workflow Management
* Codeunit 1521 - Workflow Response Handling
* Codeunit 1531 - Workflow Change Rec Mgt.
* Codeunit 1535 - Approvals Mgmt.

# Related Topics

OO Facade [https://en.wikipedia.org/wiki/Facade\_pattern][anchor8]

Argument Table pattern - [https://community.dynamics.com/nav/w/designpatterns/245.argument-table-pattern][anchor9]

Rules Table pattern

< --\[if supportAnnotations\]--\>
< --\[endif\]--\>



[anchor0]: picture1.png
[anchor1]: Picture2.png
[anchor2]: Picture3.png
[anchor3]: Picture4.png
[anchor4]: Picture5.png
[anchor5]: Picture6.png
[anchor6]: Picture7.png
[anchor7]: Picture8.png
[anchor8]: https://en.wikipedia.org/wiki/Facade_pattern
[anchor9]: /nav/w/designpatterns/245.argument-table-pattern


[image0]: picture1.png
[image1]: Picture2.png
[image2]: Picture3.png
[image3]: Picture4.png
[image4]: Picture5.png
[image5]: Picture6.png
[image6]: Picture7.png
[image7]: Picture8.png
