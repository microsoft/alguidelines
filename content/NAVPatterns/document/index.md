+++
title = "document.md"
weight = 510
+++
_By Xavier Garonnat, knk Ingénierie (France), xgaronnat@knk.fr_

## Abstract

A document structure contains a header and a set of lines. Each line is linked to the header and could have common data with header.

## Description

This pattern should be used as a basis to build any document, showing a header and multiple lines in the same page. Basically, a document is at least composed of two tables and three pages, as shown below:

[![ ][image0]][anchor0]

## Usage

You should use it any time you have to capture and store a document.

## Example 

To build this example from scratch, you will need:

* Two tables, one for the header (called "Document Header"), and one for the document lines (called "Document Line"). Each document will be composed of "1 to N" line(s).
* Three pages, one for the header, one for the subpage (lines), and the last for the document list obviously.Table "Document Header"

**Table "Document Header" **: Is the "header" table of your document (like Sales Header, Purchase Header, Transfer Header ...) 

* Add a field "No." (Code 20): Should be the first field and primary key of your documents, to be driven by Serial No. (See corresponding design pattern)

For this sample, I just added a "Sell-to Customer No." to this table. Don't forget to manage deletion of lines with trigger OnDelete().

**Table "Document Line"**: will store the lines of the document 

* Add a field "Document No." (Code 20): Should be the first field and is related to table "Document Header": set TableRelation to your "Document Header" table
* Add a field "Line No." (Integer): this field will be populated automatically by the subpage Page (see AutoSplitKey)

First (Primary) Key must be "Document No.,Line No.". On table properties, set PasteIsValid to No (to avoid copying/pasting lines, will be implemented by "Copy document", another pattern).

For my sample, I just add a couple of fields: "Item No." and "Quantity" to this table (just copy/paste standard fields from "Sales Line" table and delete trigger code, this will insure that each field will be well designed)

**Page "Document Subpage"**: will display the lines in the main form, and will be in charge of assigning line number automatically.

Create the page for table "Document Line" with the wizard by selecting the ListPart template, add all yours fields except the primary key ("Document No." and "Line No.").  
  
Then edit the properties: 

* Set AutoSplitKey, DelayedInsert and MultipleNewLines to Yes: this combination will make your subpage work as required. 
* AutoSplitKey is used to set NAV calculate the last field of the key ("Line No.") with proper numbers (10000, 20000...).

Set caption to "Lines". Save your page, we will use it on the next step.

**Page "Document"**: will display the document, and the lines with subpage.

Create the page for "Document Header" Table with the wizard by selecting the Document template:

* Add a General FastTab
* Add all the revelant fields for the user (or at least "No.")
* Click Finish to close the wizard

Then simply add your subpage as new line in the designer, and adjust the property "SubFormPerLink" with "Document No.=FIELD(No.)" to link header and lines :

**[![ ][image1]][anchor1]**

**Page "Document List": **Use the Page wizard to create a List page based on the Document table and add fields, FactBox (RecordLinks, Notes...), etc. 

Once created:

* Set Editable to No on the List
* CardPageID to Page "Document" to enable New/Edit/... Pane actions.

Save our page and add it to the Role Page "Order Processor Role Center" for example. 

Now, observe how "Line No." is calculated on the first line, and when inserting a new line between the first and second one.

Code sample (copy link to your browser) : https://knk1fr-my.sharepoint.com/personal/xgaronnat\_knk\_fr/\_layouts/15/guestaccess.aspx?guestaccesstoken=hL0P%2fyQ1ZreY5KlSPc%2b8dHrO4zjUkqQbg8DnGSbgd1Y%3d&docid=02b3cb93e1ff1459380891795fb8441fc

## NAV Usages

So many: Sales Order, Purchase Order, Transfer Order, Assembly Order...

For posted document, it's quite similar, but you don't have to setup subpage properties like AutoSplitKey, used for data entry purpose only (and your pages content should be mainly read-only / non editable).

## Ideas for improvement

* A new property like "AutoSplitStartNumber", enabled if AutoSplitKey=Yes, default value with <10000\>. Allow to change the numbers of created line.
* Be able to copy/paste header AND lines or import header and line from an Excel file.

## When it should not be used

This pattern is mainly used for Documents, and may not be used directly for Master data or any other table (Setup, Supplemental, etc...).

## Related Topics

Use Series No. Pattern for your documents, and Copy Document to implement document duplication.

## References

Walkthrough: Creating a Document Page : [http://msdn.microsoft.com/en-us/library/dd338599(v=nav.71).aspx][anchor2]  
[][anchor2][watch?v=S9cRD2D4c0&list=PLhZ3P LY7CqmVszuvtJLujFyHpsVN0Uw&index=27][anchor3]



[anchor0]: 0005.Document-Pattern-UML-Class-Diagram.jpg
[anchor1]: 2086.Design-Pattern-Document-SubPage-Properties.png
[anchor2]: http://msdn.microsoft.com/en-us/library/dd338599(v=nav.71).aspx "http://msdn.microsoft.com/en-us/library/dd338599(v=nav.71).aspx"
[anchor3]: https://www.youtube.com/watch?v=S9cRD2D4c_0&list=PLhZ3P-LY7CqmVszuvtJLujFyHpsVN0U_w&index=27


[image0]: 0005.Document-Pattern-UML-Class-Diagram.jpg
[image1]: 2086.Design-Pattern-Document-SubPage-Properties.png
