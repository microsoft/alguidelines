+++
title = "Temporary Dataset Report"
weight = 1190
+++
_by Abhishek Ghosh, at Microsoft Development Center Copenhagen_

Abstract

This pattern generates the data to be displayed dynamically by combing/processing several data sources. It then displays the resulting dataset without writing to the database.

[![ ][image0]][anchor0][][anchor1]

Description

While writing reports in NAV, we have the luxury of using a built-in iterator. So, once we define the dataitem and the ordering, the runtime takes care of the iteration.

The iterator has one shortcoming: It can only run through records written into the database. There are situations, however, where we want to display temporary datasets created at runtime by processing data from different sources. That is where the Temporary Dataset Report pattern can be used.

Usage

This pattern takes a two-step approach to displaying the data:

* Parse the data sources to create a record buffer in a temporary record variable.

* Iterate through a dataitem of the Integer table and display one record from the temporary recordset in each iteration.

Step 1: Combining data sources to create a dataset

In this step, we would process the existing data to create a temporary recordset. The three most common techniques to do this are discussed in the following paragraphs.

The first technique is mostly used when we want to build the report based on one or more source tables. A lot of processing is required, and we therefore want to store and display information from a temporary recordset. With this technique, we create a dataitem of the source record and then iterate through this dataitem to create the temporary recordset. An advantage of this technique is that it allows the user to perform additional filtering on the data source tables since they are added as additional dataitems and therefore will have their tabs on the request page by default.

[![ ][image1]][anchor2][][anchor3]

The second technique was made available with NAV 2013 when queries were introduced as a tool to help us combine data from different sources. Instead of writing data into a temporary record variable, we can create a query to combine the data from different sources. This offers better performance than the first technique in almost every situation. However, with a query, we sacrifice the luxury of getting a flexible filtering on the request page for the source dataitem.

The third technique is to write a function (or a codeunit, if the complexity demands so) that will crunch the data and create the temporary record variable. This function must be invoked from the OnPreReport trigger (or the OnPreDataItem trigger of the Integer dataitem).

[![ ][image2]][anchor4]

The following table summarizes when to use each of the three techniques:

Technique

When to Use

Source Record DataItem

When it is important to offer flexible filtering possibilities on the source data.

Query

When performance is critical.

Populating temporary table in a function

Only when the source dataset is too complex to use either of the other techniques.

Step 2: Iterating through the Integer dataitem 

When you have created the dataset as a temporary record variable or a query, the next step is to iterate through them to display the information. However, the report controller in NAV cannot iterate through temporary records or through the results of a query. This is where the Integer table comes into use.

The Integer table is a virtual table with only one field, Number, which is an integer field. For all practical purposes, we can assume that this table has pre-inserted records with value of Number, starting from 1 and ending with the highest integer value. So, if we keep iterating through this dataitem, the value of the Number field will be 1 for the first iteration and will keep increasing by 1 for every iteration.

There are two ways to use the Integer dataitem to iterate through the temporary recordset:

* Loop for an indeterminate number of times until you reach the end of the recordset.

* Calculate the number of records in the temporary recordset in advance and then iterate that many number of times. 

With the first approach, in the Integer-OnAfterGetRecord trigger, you must make the temporary record variable move to the next record, follow that up with a check if you have reached the end of the recordset, and then use CurrReport.BREAK if you have reached the end.

With the second approach, you must limit the number of times the report iterates through the Integer dataitem. The Number field in the Integer table will have a value of 1 in the first iteration and will increment automatically by 1 on every subsequent iteration. To ensure that the report iterates through the Integer dataitem "n" times, you must, therefore, filter the Integer dataitem on Number = 1..n. You must also move the temporary record variable to the next record in every iteration of the Integer-OnAfterGetRecord trigger. NOTE: This technique cannot be used if you use queries as the temporary data source, since queries do not (in NAV 2013) return the number of records.

Example 

Goal: To print a report that lists all contacts that have open documents (both sales and purchase) and displays those documents in chronological order

A contact can be connected to a vendor or a customer using the Create As Customer/Vendor function on the Contact Card. When a sales/purchase document is created for this customer/vendor, the Contact No. is stored in the Sell-to Contact No. or Buy-from Contact No. fields of the sales and purchase documents. The obvious choice would be to display the data directly from the Sales Header and Purchase Header records. However, the requirement is to display all of them together chronologically, which means they will need to be stored in, and be read from, one common dataset.

Step 1: Design a new table that will hold the temporary dataset.

The UI must display the Document Type, Document No., Document Date, Amount Incl. VAT and the name of the salesperson or purchaser. The table is designed as follows.

[![ ][image3]][anchor5]

Since this table is used to sort the data by Document Date, a "Document Date" key is added to the table.

[![ ][image4]][anchor6]

Some additional properties to note:

* Sales Header dataitem should be linked to the Contact dataitem through the DataItemLink property -- "Sell-to Contact No.=FIELD(No.)"

* Purchase Header dataitem should be linked to the Contact dataitem through the DataItemLink property -- "Buy-from Contact No.=FIELD(No.)"

Step 2: In the Sales Header -- OnAfterGetRecord, write the following code to populate the data buffer:

[![ ][image5]][anchor7]

Where:

* ContactDocumentBuffer is a temporary global variable of the buffer table created.

* SalesPerson is a local record variable for table 13\.
* SalesTxt is a text constant denoting "Sales" in ENU.

Similar code must be written in Purchase Header -- OnAfterGetRecord to store data from the purchase documents into the buffer.

Step 3: Iterate through the temporary records in the Integer dataitem.

Since the requirement is to sort the data by Document Date, we must sort the ContactDocumentBuffer table by this key. Also, by the time the execution of Integer -- OnPreDataItem begins, the number of records in the temporary buffer should be known already. Therefore, we can limit the number of times to repeat the Integer dataitem to the number of records in the buffer.

We meet both requirements with the following lines of code in Integer - OnPreDataItem:

ContactDocumentBuffer.SETCURRENTKEY("Document Date"); 

SETRANGE(Number,1,ContactDocumentBuffer.COUNT);

Lastly, we must move the record pointer by 1 record every time we loop through the Integer dataitem. So, in Integer -- OnAfterGetRecord, we add the following lines of code:

IF Number = 1 THEN

ContactDocumentBuffer.FINDFIRST

ELSE

ContactDocumentBuffer.NEXT;

Now, the only task that remains is to design the RDLC layout. (Not part of this application pattern description.)

NAV Usages

This is a commonly used pattern in several reports, such as:

* Report 204 -- Sales -- Quote

* Report 205 -- Order Confirmation

* Report 206 -- Sales -- Invoice 

[watch?v=QHn5oEOJv0Q&list=PLhZ3P LY7CqmVszuvtJLujFyHpsVN0Uw&index=10][anchor8]

* * 

[anchor0]: /cfs-file.ashx/__key/communityserver-wikis-components-files/00-00-00-00-42/0250.Temporary-Dataset-Report-1.gif
[anchor1]: /cfs-file.ashx/__key/communityserver-wikis-components-files/00-00-00-00-42/8424.Temporary-Dataset-Report-1.gif
[anchor2]: /cfs-file.ashx/__key/communityserver-wikis-components-files/00-00-00-00-42/2376.Temporary-Dataset-Report-2.gif
[anchor3]: /cfs-file.ashx/__key/communityserver-wikis-components-files/00-00-00-00-42/8461.Temporary-Dataset-Report-2.gif
[anchor4]: /cfs-file.ashx/__key/communityserver-wikis-components-files/00-00-00-00-42/7607.Temporary-Dataset-Report-3.gif
[anchor5]: 6523.Temporary-Dataset-Report-4.jpg
[anchor6]: 4118.Temporary-Dataset-Report-5.jpg
[anchor7]: 4010.Temporary-Dataset-Report-6.jpg
[anchor8]: https://www.youtube.com/watch?v=QHn5oEOJv0Q&list=PLhZ3P-LY7CqmVszuvtJLujFyHpsVN0U_w&index=10


[image0]: /resized-image.ashx/__size/550x0/__key/communityserver-wikis-components-files/00-00-00-00-42/0250.Temporary-Dataset-Report-1.gif
[image1]: /resized-image.ashx/__size/550x0/__key/communityserver-wikis-components-files/00-00-00-00-42/2376.Temporary-Dataset-Report-2.gif
[image2]: /resized-image.ashx/__size/550x0/__key/communityserver-wikis-components-files/00-00-00-00-42/7607.Temporary-Dataset-Report-3.gif
[image3]: 6523.Temporary-Dataset-Report-4.jpg
[image4]: 4118.Temporary-Dataset-Report-5.jpg
[image5]: 4010.Temporary-Dataset-Report-6.jpg
