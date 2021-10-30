+++
title = "Feature Localization For Data Structures"
weight = 570
+++
_Originally by Bogdan Sturzoiu at Microsoft Development Center Copenhagen_ 

## Abstract

This pattern shows a solution for integrating W1 features to pre-existing country features that use different tables to achieve similar functionality.

## Description

It sometimes happens that certain features are requested in a NAV-supported country, but they are not initially considered generic enough to be included in the W1 build. This is how local features, such as Subcontracting in Italy and India, were created or specific banking and payments functionality in Italy, France, Spain, and others.

Then, at some point in time, a decision is made to create a W1 feature that is closely related to the local functionality but uses a completely different set of tables, pages, etc. The developers now face the following problem: How to enable the newly-developed W1 feature into a country, such that the customers who are accustomed to their local structures can seamlessly continue working without completely (or immediately) switching to the W1 objects.

This was the issue that was tackled in the NAV 2013 R2, in relation to the SEPA Credit Transfers functionality.

### Using a Proxy

The generic Proxy pattern is "a class functioning as an interface to something else" ([Wikipedia][anchor0]).

[![ ][image0]][anchor1]

Figure 1\. Proxy in UML

### Pattern Elements

The NAV data model translation of the proxy pattern can be used as explained below.

The RealSubject is the NAV data model. Variations in table structures, relationships, and numbers are particular to each country. The W1 model is the base for the country-localized data models. However, some countries have heavy localizations which cannot be directly processed by the W1 core objects.

The proxy is a codeunit that gathers data from wherever it is stored and transforms it to fit into a standard table, which is later used across all localizations.

The interface is the fixed form in which the data is presented to be consumed by the client.

The client can be an XML port that is fed from the common data interface. It can also be any other data processor (a codeunit fed to another table, etc.) or data display object (page or report).

### Pattern Steps

1. The user creates records in the local tables.
2. The user invokes an action that must be processed using the W1 feature code.

1. The proxy codeunit moves the data from the local tables to the W1 tables, either into a temporary or persistent set of records, as needed.

1. The W1 code now performs the action on the W1 table data. 

## Usage

In NAV 2013 R2, we released the SEPA Credit Transfer functionality. It involves exporting vendor payments to an XML file that is subsequently processed by the customer's bank. The payments are exported from the Payment Journal page through a configurable XMLport. Therefore, the data source for these payment lines is the Gen. Journal Line table (81).

In various countries, we already had payment export functionality, usually into flat bank files. However, the files are generated from different tables than in W1\. For example, in Italy, vendor payments are handled through the Vendor Bill Header table (12181) and the Vendor Bill Line table (12182). They are the RealSubject.

The W1 feature flow is as follows:

[![ ][image1]][anchor2]

Figure 2\. W1 object call sequence

**Note:** CT = Credit Transfers, pain = payments initiation (the XML format used for SEPA Credit Transfers and SEPA Direct Debit).

The key question is: Where to tap into this flow when using a local data structure. For this purpose, a proxy codeunit has been added in W1, called 1222 -- SEPA CT-Prepare Source. This codeunit feeds the client (XML1000) data in a standard format (the interface is the Gen. Journal Line table (81)). 

In W1, the codeunit simply outputs the same set of general journal lines that it receives as an input:

OnRun(VAR Rec : Record "Gen. Journal Line")

GenJnlLine.COPYFILTERS(Rec);

CopyJnlLines(GenJnlLine,Rec);

LOCAL CopyJnlLines(VAR FromGenJnlLine : Record "Gen. Journal Line";VAR TempGenJnlLine : TEMPORARY Record "Gen. Journal Line")

IF FromGenJnlLine.FINDSET THEN BEGIN

GenJnlBatch.GET(FromGenJnlLine."Journal Template Name",FromGenJnlLine."Journal Batch Name");

REPEAT

TempGenJnlLine := FromGenJnlLine;

TempGenJnlLine.INSERT;

UNTIL FromGenJnlLine.NEXT = 0

END ELSE

CreateTempJnlLines(FromGenJnlLine,TempGenJnlLine);

LOCAL CreateTempJnlLines(VAR FromGenJnlLine : Record "Gen. Journal Line";VAR TempGenJnlLine : TEMPORARY Record "Gen. Journal Line")

// To fill TempGenJnlLine from the source identified by filters set on FromGenJnlLine

TempGenJnlLine := FromGenJnlLine;

In a country, such as Italy, the codeunit will have the following functions:

1. Gets an empty set of general journal lines that carry the local payment document key as a filter on the Document No. field (as opposed to W1 that gets the real set of records to be exported). This is done so that the local data can be extracted at runtime.

1. Selects the local payment data, for example in Italy, in the Vendor Bill Header and Vendor Bill Lines tables.

1. Transforms the local payment data into temporary records of the Gen. Journal Line table.

1. Outputs the temporary general journal lines that will be further processed and exported, exactly as in W1\.

OnRun(VAR Rec : Record "Gen. Journal Line")

GenJnlLine.COPYFILTERS(Rec);

CopyJnlLines(GenJnlLine,Rec);

LOCAL CopyJnlLines(VAR FromGenJnlLine : Record "Gen. Journal Line";VAR TempGenJnlLine : TEMPORARY Record "Gen. Journal Line")

IF FromGenJnlLine.FINDSET THEN BEGIN

GenJnlBatch.GET(FromGenJnlLine."Journal Template Name",FromGenJnlLine."Journal Batch Name");

REPEAT

TempGenJnlLine := FromGenJnlLine;

TempGenJnlLine.INSERT;

UNTIL FromGenJnlLine.NEXT = 0

END ELSE

CreateTempJnlLines(FromGenJnlLine,TempGenJnlLine);

LOCAL CreateTempJnlLines(VAR FromGenJnlLine : Record "Gen. Journal Line";VAR TempGenJnlLine : TEMPORARY Record "Gen. Journal Line")

PaymentDocNo := FromGenJnlLine.GETFILTER("Document No.");

VendorBillHeader.GET(PaymentDocNo);

VendorBillLine.RESET;

VendorBillLine.SETCURRENTKEY("Vendor Bill List No.","Vendor No.","Due Date","Vendor Bank Acc. No.","Cumulative Transfers");

VendorBillLine.SETRANGE("Vendor Bill List No.",VendorBillHeader."No.");

VendorBillLine.SETRANGE("Cumulative Transfers",TRUE);

IF VendorBillLine.FINDSET THEN BEGIN

CumulativeAmount := 0;

PrevVendorBillLine := VendorBillLine;

REPEAT

VendorBillLine.TESTFIELD("Document Type",VendorBillLine."Document Type"::Invoice);

IF ((VendorBillLine."Vendor No." <\> PrevVendorBillLine."Vendor No.") OR (VendorBillLine."Vendor Bank Acc. No." <\> PrevVendorBillLine."Vendor Bank Acc. No.")) THEN BEGIN InsertTempGenJnlLine(TempGenJnlLine,VendorBillHeader,PrevVendorBillLine,CumulativeAmount);

CumulativeAmount := VendorBillLine."Amount to Pay";

END ELSE

CumulativeAmount += VendorBillLine."Amount to Pay";

PrevVendorBillLine := VendorBillLine;

UNTIL VendorBillLine.NEXT = 0; InsertTempGenJnlLine(TempGenJnlLine,VendorBillHeader,PrevVendorBillLine,CumulativeAmount);

END;

VendorBillLine.SETRANGE("Cumulative Transfers",FALSE);

IF VendorBillLine.FINDSET THEN

REPEAT

VendorBillLine.TESTFIELD("Document Type",VendorBillLine."Document Type"::Invoice); InsertTempGenJnlLine(TempGenJnlLine,VendorBillHeader,VendorBillLine,VendorBillLine."Amount to Pay");

UNTIL VendorBillLine.NEXT = 0;

LOCAL InsertTempGenJnlLine(VAR TempGenJnlLine : TEMPORARY Record "Gen. Journal Line";VendorBillHeader : Record "Vendor Bill Header";VendorBillLine : Record "Vendor Bill Line";AmountToPay : Decimal)

WITH TempGenJnlLine DO BEGIN

INIT;

"Journal Template Name" := '';

"Journal Batch Name" := '';

"Document Type" := "Document Type"::Payment;

"Document No." := VendorBillLine."Vendor Bill List No.";

"Line No." := VendorBillLine."Line No.";

"Account No." := VendorBillLine."Vendor No.";

"Account Type" := TempGenJnlLine."Account Type"::Vendor;

"Bal. Account Type" := TempGenJnlLine."Bal. Account Type"::"Bank Account";

"Bal. Account No." := VendorBillHeader."Bank Account No.";

"Applies-to Ext. Doc. No." := VendorBillLine."External Document No.";

Amount := AmountToPay;

"Applies-to Doc. Type" := VendorBillLine."Document Type";

"Applies-to Doc. No." := VendorBillLine."Document No.";

"Currency Code" := VendorBillHeader."Currency Code";

"Due Date" := VendorBillLine."Due Date";

"Posting Date" := VendorBillHeader."Posting Date";

"Recipient Bank Account" := VendorBillLine."Vendor Bank Acc. No.";

Description := VendorBillLine.Description;

"Message to Recipient" := VendorBillLine."Description 2";

INSERT;

END;

The derived local feature flow is as follows:

[![ ][image2]][anchor3]

Figure 3\. The local country object flow

As we can see from the diagram, this solution allows integration of the local and W1 features with a minimum amount of changes in W1 code. The only two differences are:

1. The entry point of the flow is the local table/page.

1. Codeunit 1222 is overloaded to prepare general journal lines from the local records.

## NAV Usages

The data mapping technique has been used for the SEPA Credit Transfer feature, and will be used in subsequent local integration projects.

## Ideas for improvement

A weak point for this pattern is the need to set a filter on the empty journal line in order to retrieve the local data when exporting from a local page. This can cause problems if the size of the local table document number is larger than the Document No. field (ID 20) in the Gen. Journal line table (81).

Also, there is a strong need for thorough testing when using this pattern, because there might be differences in the behavior of the local table and table 81\. Whatever is acceptable for the local table may not be acceptable for the W1 table. A deep functional analysis is needed to see if the local export feature uses the same constraints as the W1 feature.



[anchor0]: http://en.wikipedia.org/wiki/Proxy_pattern
[anchor1]: 5123.Feature-localization-for-data-structures-1.png
[anchor2]: 6052.Feature-localization-for-data-structures-2.png
[anchor3]: 3058.Feature-localization-for-data-structures-3.png


[image0]: 5123.Feature-localization-for-data-structures-1.png
[image1]: 6052.Feature-localization-for-data-structures-2.png
[image2]: 3058.Feature-localization-for-data-structures-3.png
