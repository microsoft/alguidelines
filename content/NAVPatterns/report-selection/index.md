+++
title = "Report Selection"
weight = 1010
+++
From the PRS workshop at NAVTechDays 2013, this pattern was written by 2 work groups

Group 1: Dale Gauci & Kimberly Congleton

Group 2: Jens Winberg, Tim Grant, Alen Tot

Thanks to Tim Grant who had merged the two patterns, corrected, sent for review and finalized them.

## **Meet the Pattern**

It should be possible to change which document report object should be used when printing. This configuration can be stored in one place (Report Selection) and available at configuration time. In this situation, the code for printing records can remain the same.

The purpose of this pattern is to describe the process to implement a reports selection by retrieving the specific **Document Header**, determining which type of document is related to it in the **Report Selection Screen**, and call the report which needs to be printed by passing the specific document header. This specifies the reports that you can print when you work with the various documents for sales and purchases, such as orders, quotes, invoices, and credit memos.

## **Know the Pattern**

The program can preselect which report will be printed when you print from various types of purchase and sales headers. For example, on an order, the **Order Confirmation** report is automatically printed.

The **Report Selection** table contains the specification of which report will be printed in different situations. The **Report Selection** table also contains the report ID and report name for the report that will be printed when the user works with a given document type.

The user can, of course, choose to have the program print a different report than the preselected one. You can also add reports to the **Report Selection** table to have the program print more than one report per document type.

This pattern should be used when the user needs to print a new type of document which can have different report layouts, or when the user needs to print several different reports in sequence. This can also be used during a new post and print routine, a test report for journal posting or for re-printing a posted document. Using this pattern will minimize code by allowing a flexible means of specifying different reports to print, instead of hard-coding a report id when printing a document.

## **Use the Pattern**

The Report Selection Pattern involves the **Report Selection** table as the central configuration element also with the **Report Selection** Page. The table is used in 4 generic functional workflows:

1) Test Print un-posted

If the Report Selection is related to a Test Report relating to a Document:

* The Document's Page including Printing Actions
* Test Report-Print Codeunit 228

2) Print un-posted. If the Report Selection is related to an existing Document (un-posted):

* The Document's Page (Document / List) including the Printing Actions
* The Document-Print Codeunit 229 is used atomically to use the document type in the generic Sales Header table before the report selection is found.

3) Print while posting. If the Document is printed at the point of posting then:

* The Document's Post + Print Codeunit (82 or 92), with the related **GetReport** and **PrintReport** functions.

4) Print after posting. If the Report Selection is related to an existing Document (posted):

* The specific document related posted header table is used along with a typical function: **PrintRecords **within the respective table.

## **Example**

Sales Document (Invoice)

Pre-conditions

* The document header/lines table is present and there is a report which has a parent DataItem linked to this document.
* A document and list page is present and related to the document.
* A post and print Codeunit exists for the document.

Implementation:

* Add a new **Option String** to the **Report Selection** table, **Usage** field (including any ML Captions).
* Calling of **GetReport** function, passing the document header.

    SalesSetup.GET;
    IF SalesSetup."Post & Print with ob Queue" THEN
    SalesPostViaobQueue.EnqueueSalesDoc(SalesHeader)
    ELSE BEGIN
    CODEUNIT.RUN(CODEUNIT::"Sales-Post",SalesHeader);
    GetReport(SalesHeader);
    END;
    COMMIT;

* Implementing the new document type within the **GetReport** Function, and calling **PrintReport** with the New OptionString

    "Document Type"::Invoice:
    BEGIN
    IF "Last Posting No." = '' THEN
    SalesInvHeader."No.":= "No."
    ELSE
    SalesInvHeader."No." := "Last Posting No.";
    SalesInvHeader.SETRECFILTER;
    PrintReport(ReportSelection.Usage::"S.Invoice");
    END;

* Implement the new Document Type and calling the associated report ID, passing the document header

## **NAV Usages**

* Codeunits 82, 92, for post and printing purchase and sales documents
* Codeunit 229 for Document printing
* Header Tables 110, 112, 114, 120, 122, 124, 295, 297, 302, 304\. 5744, 5746, 6650, 6660

This pattern is already used for printing documents like sales invoices, proforma, waybills, Finance Charge Memos, Receipt Documents.

****

## **Ideas for improvement**

The "Usage" Field could be more dynamic through a setup field linked to a document type table mapped to the document header Table ID.

Report Selection table is currently based on the Usage of the report to be defined. A more generic way could be to only select what record id the printing selection is based on could also be implemented. (I think this means Recordref and Table Number could be used to identify the posted tables)

The Report Selection matrix could be evolved to accommodate the Journal post & print configuration. This setup currently resides in the Gen. Journal Template table.

Merge the **PrintReport** functions in the purchase and sales post printing functions (82, 92) into a more atomic print selection component.

Merge the **PrintRecords** functions in the separate posted header tables into a more atomic print selection component.

## **Related Patterns**

* Posting Routine Pattern (Journal/Document)
* Post Batch Routine Pattern
