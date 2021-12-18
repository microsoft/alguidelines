+++
title = "Journal Error Processing"
weight = 710
+++
_Originally by Bogdana Botez at Microsoft Development Center Copenhagen_ 

## Abstract

This pattern describes an optimized way to handle invalid, incomplete, or inconsistent data that users enter in journals.

## Description

Scenario: A user has entered data on a journal line and proceeds to invoke a processing action on it, such as posting or exporting to electronic payments. NAV validates the data before it is committed. If any validation errors are found, the user must be informed of validation errors in the most optimal way.

One design is that when an error is found, stop execution and prompt the user to correct the error. After correcting the error, the user restarts processing and is stopped again at the next error, and so on. Stopping and showing each error is time-consuming and frustrating for the user. 

Another design is that processing does not stop when an error is found. Instead, all errors are gathered in a table and displayed all at once at the end of processing. This way, the processing is ideally invoked only once, reducing the time and effort spent by the user to expose and correct all data validation errors.

In both designs, the processing is not finalized if any errors are found (for example, exporting to electronic payments is not done, until the data error is resolved).

This document describes how to implement the second error-handling design: Showing all errors at the end.

## Usage

The example below comes from the implementation of SEPA Credit Transfer.

After setting up SEPA-specific configurations, the user can start entering vendor payments that will later be exported to the payment file. (The setup depends on the country, but generally involves choosing number series for SEPA export files, choosing the export format, and enabling SEPA Credit Transfer.)

In the W1 solution (and most of the countries), payment lines are created in the Payment Journal page, from where the user can invoke the Export Payments to File action, which will attempt to create a SEPA-compliant XML file containing the description of the journal payments that are to be made by the bank.

When the Export Payments to File function is invoked, NAV validates the journal line data. If the data must be completed or updated, then no file will be created and the user sees the following message:

[![ ][image0]][anchor0]

To give a visual overview, the lines that need corrections are highlighted in red. The factbox is context-sensitive, meaning that it shows only the errors that relate to the currently selected line.

When the first payment journal line is selected, the FactBox show errors for the first line.

[![ ][image1]][anchor1]

When the second payment journal line is selected, the FactBox shows errors for the second line. 

[![ ][image2]][anchor2]

## Application Objects

In the following table, the Generic Object column contains the objects that you can use as a base for your implementation.

Generic Object

Description

Sample W1 implementation of SEPA Credit Transfer\*

Journal Page

This is the journal list page where the user invokes the processing action.

Payment Journal

Action on Page

The processing action invoked by the user on the journal list page.

Export Payments to File

Errors Page List Part

A FactBox that displays any journal line validation errors.

To improve user experience, the developer can highlight the lines with errors in red and conveniently sort the lines with errors at the top.

Payment Journal Errors Part

Validation codeunit

Contains code that checks that the journal line contains correct, complete, and coherent data and that the line is ready for whatever process must be done next.

SEPA CT-Check Line

Processing codeunit

Executes the processing of the journal lines.

SEPA CT-Export File

Journal Error Text Table

Contains

* The error messages

* Link information about where the error messages belong. For example, in table 1228, Payment Jnl. Export Error Text, the error is linked uniquely to a journal line by the following fields:

* Journal Template Name, with TableRelation="Gen. Journal Template"
* Journal Batch Name, with TableRelation="Gen. Journal Batch".Name WHERE (Journal Template Name=FIELD(Journal Template Name))
* Journal Line No.

Other related information can be added, such as document number of the original source document, if the current journal line originates from a document.

An extra improvement would be to add a drilldown or a link to the page where the user can fix the error. This would significantly simplify the scenario by excluding manual navigation and investigation by the user to find the page where the error can be fixed.

Payment Jnl. Export Error Text

\* The W1 implementation of file export for SEPA Credit Transfer contains the generic SEPA functionality. However, due to differences in data models and user scenarios in various country implementations, the selected local versions contain adaptations of the generic functionality.

## Flow

Find below a diagram describing the flow between the objects involved in the journal error processing.

[![ ][image3]][anchor3]

## Code

Following the flow above, the code (in the SEPA Credit Transfer example) is as follows.

[![ ][image4]][anchor4]

The public interface of this table contains simple functionality for adding/deleting errors and for interrogation on if any errors are associated with the current journal template and batch.

```AL
CreateNew(GenJnlLine : Record "Gen. Journal Line";NewText : Text)

SetLineFilters(GenJnlLine);

IF FINDLAST THEN;

"Journal Template Name" := GenJnlLine."Journal Template Name";

"Journal Batch Name" := GenJnlLine."Journal Batch Name";

"Document No." := GenJnlLine."Document No.";

"Journal Line No." := GenJnlLine."Line No.";

"Line No." += 1;

"Error Text" := COPYSTR(NewText,1,MAXSTRLEN("Error Text"));

INSERT;

JnlLineHasErrors(GenJnlLine : Record "Gen. Journal Line") : Boolean

SetLineFilters(GenJnlLine);

EXIT(NOT ISEMPTY);

JnlBatchHasErrors(GenJnlLine : Record "Gen. Journal Line") : Boolean

SetBatchFilters(GenJnlLine);

EXIT(NOT ISEMPTY);

DeleteJnlLineErrors(GenJnlLine : Record "Gen. Journal Line")

IF JnlLineHasErrors(GenJnlLine) THEN

DELETEALL;

DeleteJnlBatchErrors(GenJnlLine : Record "Gen. Journal Line")

IF JnlBatchHasErrors(GenJnlLine) THEN

DELETEALL;
```

## NAV Usages 

* SEPA Credit Transfer feature - for export of vendor payments

* SEPA Direct Debit feature for export of customer payment instructions

The same concept of storing error messages (but with a different flow) is also present in:

* Planning Error Log table (5430) - Supply Planning feature

* Costing table (5890) - Costing feature

## Ideas for Improvement

Older code in NAV does not use this pattern yet. It would be good for consistency reasons, and also for overall user experience, to extend this pattern to replace the error processing in more areas.

We can also improve by helping users find the place where they must fix the error by providing auto-navigation to the required page.



[anchor0]: 5518.Journal-Error-Processing-1.jpg
[anchor1]: 0005.Journal-Error-Processing-2.jpg
[anchor2]: 8640.Journal-Error-Processing-3.jpg
[anchor3]: 0777.Journal-Error-Processing.jpg
[anchor4]: 5661.Journal-Error-Processing-8.jpg


[image0]: 5518.Journal-Error-Processing-1.jpg
[image1]: 0005.Journal-Error-Processing-2.jpg
[image2]: 8640.Journal-Error-Processing-3.jpg
[image3]: 0777.Journal-Error-Processing.jpg
[image4]: 5661.Journal-Error-Processing-8.jpg
