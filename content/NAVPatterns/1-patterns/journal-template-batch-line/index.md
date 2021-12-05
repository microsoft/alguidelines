+++
title = "Journal Template Batch Line"
weight = 720
+++
_Originally by Bogdana Botez at Microsoft Development Center Copenhagen_

## Abstract

The role of a journal line is to temporarily hold transaction data until the transaction is posted. Before posting, the entries are in a draft state, which means that they are available for corrections and/or deletion. As soon as the entries are posted, they are converted to ledger entries.

Journal templates are used to specify the underlying journal structure and to provide the default information for the journal batches. Journal batches usually serve to group journal lines, such as lines created by two different users.

_[![ ][image0]][anchor0]_

## Description

Journal templates and journal batches are used if there is a need to create and post one or more entries. They are implemented in multiple areas of the application, like Sales, Purchases, Cash Receipts, Payments, Fixed Assets1.

### Journal Templates 

The journal templates are located on the Journal Template page. A Journal Template definition contains a series of attributes, such as:

* Name
* Description
* Type
* Recurring
* No. Series

The Journal Template table stores the relevant attributes that define the nature and behavior of the journal templates, for example:

Journal Template Table Field

Description

Test Report ID

The journals offer the possibility of running test reports3. The role of a test report is to simulate the posting process. The verification criteria for the journal lines is ran, and the report can be displayed, all without doing the actual posting. This helps finding and correcting any errors that might exist in the data.

The name of the test report is the same with the name of the corresponding journal, plus the suffix " - Test". For example, the General Journal has the associated test report named General Journal - Test.

Posting Report ID

This report is printed when a user selects Post and Print4.

Page ID

For some journals, more UI objects are required. For example, the General Journals have a special page for bank and cash.

Source Code

Here you can enter a Trail Code for all the postings done through this Journal4.

Recurring

Whenever you post lines from a recurring journal, new lines are automatically created with a posting date defined in the recurring date formula.

Each journal template defines a default value of those attributes. The values that are defined in a template will be inherited by the journal batches, which will be created from a journal template.

Microsoft Dynamics NAV is released with a number of standard journal templates predefined in the Journal Templates page. More templates can be defined by the users.

### Journal Batches

Journal batches are created with the help of the journal templates.

A journal batch is typically used to make a distinction between collections of logically grouped journal lines. A typical design is to have a journal batch for each user who enters lines. The batches are used during the posting process, in order to post one or multiple lines at once.

### Journal Lines

Journal lines contain the actual business data (posting dates, account numbers, amounts) that will be posted as ledger entries.

During posting, only the information from the journal lines is needed. However, the information has been created with the help of the journal templates and grouped together using the journal batches.

Posting creates ledger entries from the temporary content that is stored in the journal lines. Ledger entries are not created directly. Instead, they are posted from journal lines.

_[![ ][image1]][anchor1]_

### Aggregation

There is a 1:n aggregation relationship between journal templates and journal batches, as well as between journal batches and journal lines. Deleting a template will cascade deletion of the related batches and lines. Deleting a batch will cascade into deletion of related lines.

### Recurring Journals

A recurring journal is used to post transactions that repeat periodically. In a recurring journal, the user enters only the variable data, such as posting date, amounts, and accounts to be used for posting.

After posting a recurring journal, new journal lines are created containing the posting date for the next recurring period. The posting date recurrence pattern is previously defined in the Recurring Frequency field (for example, monthly recurrences are defined with the date formula 1M).

A boolean field named Recurring is placed on both the journal templates and journal batches, giving the possibility of defining the type of the journal to be used.

### Consistent User Experience

To keep a consistent user interface experience, it is recommended that the the following guidelines are taken respected concerning navigation:

* Journal template to journal batches:

* On the Journal Templates page, create an action called "Batches" and place it in the Navigate tab of the ribbon. Link the action to the batches list page.

* Journal batch to journal lines:

* On the Batch page, create an action called "Edit Journal" in the Home ribbon tab. Link the action to the journal lines list page.

To keep a consistent user interface experience, it is recommended that the the following guidelines are taken respected concerning posting:

* Posting multiple batches

* On the Journal Batches page, posting actions (Post, Post and Print) are available. When invoked, the batch posting will iterate through all related journal lines and trigger the posting routine for all of the lines.

## Usage

### General Journals

The General Journal Templates page (101) uses the Gen. Journal Template table (80).

Various template types are defined: General,Sales,Purchases,Cash Receipts,Payments,Assets,Intercompany,Jobs. Based on the journal type, two other attributes are automatically set on the template lines as follows:

* Page ID: Defines which journal page relates to the current journal template
* Source Code: Filled with the default codes that are defined in Source Code Setup table (242).

The General Journals Batches page (251) is linked to the Gen. Journal Batch source table (232), which has a multiple-to-1 relationship with Gen. Journal Template table, based on the Journal Template Name field.

Some of the fields in the Gen. Journal Batch table are not editable. Instead, their value is automatically calculated from the parent Gen. Journal Template table. For example, the Recurring field (22) is a FlowField with the following calculation formula:

    Lookup("Gen. Journal Template".Recurring WHERE (Name=FIELD(Journal Template Name)))


Similarly, the Template Type field is a FlowField that gets its value from the parent table:

    Lookup("Gen. Journal Template".Type WHERE (Name=FIELD(Journal Template Name)))

### Setting up a New Batch

When the user creates a new batch, the following field values are transferred from the Gen. Journal Template table to the Gen. Journal Batch table:

    "Bal. Account Type" := GenJnlTemplate."Bal. Account Type";

    "Bal. Account No." := GenJnlTemplate."Bal. Account No.";

    "No. Series" := GenJnlTemplate."No. Series";

    "Posting No. Series" := GenJnlTemplate."Posting No. Series";

    "Reason Code" := GenJnlTemplate."Reason Code";

    "Copy VAT Setup to Jnl. Lines" := GenJnlTemplate."Copy VAT Setup to Jnl. Lines";

    "Allow VAT Difference" := GenJnlTemplate."Allow VAT Difference";

### Cascade record deletion 

When a record from the Gen. Journal Template table is deleted, the corresponding Gen. Journal Batch and Gen. Journal Line records are also deleted.

### Cascade updates

When the reason code or the posting number series change in the current batch, all linked Gen. Journal Line records are updated (see ModifyLines function on the Gen. Journal Batch table).

The Gen. Journal Line table (814) stores a relation with the Journal Batch Name field (51) in the Gen. Journal Batch table. The Gen. Journal Line table also inherits the table relation with the Journal Template Name field (1) in the Gen. Journal Template table.

## NAV Usages

Implementations of this pattern in NAV include:

* General Journal (see "Use the Pattern" above) 
* Item Journal
* Resource Journal
* Job Journal

References 

1. [NAV Course 50534][anchor2] Finance Essentials in Microsoft Dynamics NAV 2013, Chapter 3: "General Journals".
2. [NAV Course 50435][anchor3] Application Setup in Microsoft Dynamics NAV 2013, Chapter 5: "Set up Journal Templates and Batches"
3. [Test reports][anchor4] Definition on MSDN.
4. [Microsoft Dynamics NAV 2009: Using the journals and entries in a custom application][anchor5] Blog article by Mark Brummel
5. [Search result for "Journal+NAV" ][anchor6]Various topics on MSDN 

## Related Pattern: Standard Journal 

For cases when most of the journal data can be used later (like monthly electricity payments, for example), the user has the possibility to save the current transaction details for later use. See the related pattern, Standard Journal.

{{< youtube xtsZ5beNdZg>}}



[anchor0]: 2438.Journal-Template-Batch-Line-1.jpg
[anchor1]: 8103.Journal-Template-Batch-Line-2.jpg
[anchor2]: https://mbs.microsoft.com/partnersource/communities/training/trainingmaterials/student/course80534.htm?printpage=false
[anchor3]: https://mbs.microsoft.com/partnersource/communities/training/trainingmaterials/student/course80435.htm?printpage=false
[anchor4]: http://msdn.microsoft.com/en-us/library/dd338776.aspx
[anchor5]: http://www.packtpub.com/article/microsoft-dynamics-nav-2009-using-journals-and-entries-custom-application
[anchor6]: http://social.msdn.microsoft.com/Search/en-US?query=journals%20nav&ac=3
[anchor7]: https://www.youtube.com/watch?v=xtsZ5beNdZg&list=PLhZ3P-LY7CqmVszuvtJLujFyHpsVN0U_w&index=12


[image0]: 2438.Journal-Template-Batch-Line-1.jpg
[image1]: 8103.Journal-Template-Batch-Line-2.jpg
