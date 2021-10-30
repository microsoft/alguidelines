+++
title = "Copy Document"
weight = 390
+++
_By Bogdan Sturzoiu at Microsoft Development Center Copenhagen_ 

## Abstract

The goal of the Copy Document pattern is to create a replica of an existing open or closed document (posted or not posted), by moving the lines and, optionally, the header information from the source document to a destination document.

## Description

Documents are widely used by most of our customers. Many times, a significant portion of these documents are similar to each other, either by sharing the same customer, vendor, type, or line structure. Being able to re-use a document as a base for creating a new one is therefore an important means of saving time.

Other business scenarios require that a newly created document is applied to an existing document. For example, in returns management, a return order can be the reversal of an existing order and can therefore be copied from the original order. Other times, there is even a legal requirement to match the document to its source. For example, credit memos need to be applied to the originating Invoice.

For these reasons, NAV supports the copying of documents as a method to re-use or link documents.

The Copy Document functionality is used in the following situations:

* The user wants to create a new open sales document (Quote, Order, Blanket Order, Invoice, Return Order, Credit Memo) based on an existing posted or non-posted sales document (Quote, Blanket Order, Order, Invoice, Return Order, Credit Memo, Posted Shipment, Posted Invoice, Posted Return Receipt, Posted Credit Memo).
* The user wants to create a new open purchase document (Quote, Order, Blanket Order, Invoice, Return Order, Credit Memo) based on an existing posted or non-posted purchase document (Quote, Blanket Order, Order, Invoice, Return Order, Credit Memo, Posted Shipment, Posted Invoice, Posted Return Receipt, Posted Credit Memo).
* The user wants to create a new production order (Simulated, Planned, Firm Planned or Released) based on an existing production order (Simulated, Planned, Firm Planned, Released or Finished).
* The user wants to create a new assembly order based on an existing assembly document (Quote, Blanket Order, Order and Posted Order).
* The user wants to create a new service contract or quote based on an existing service contract or quote.
* The user wants to create all relevant return-related documents. For example, from a sales return order, the user can recreate the involved supply chain documentation, by copying the information upwards to a purchase return order (if the items need to be returned to the vendor), purchase order (if the items need to be reordered), and sales order (if the items need to be re-sent to the customer).

**Note**

* Not all to and from combinations are allowed. For example, you can only copy to open document types, since the posted documents are not editable.
* The destination document needs to have the header fully created. For example, a Sales Order will need to have the Sell-To Customer No. populated.

## Usage

The Dynamics NAV application developer can take into account using the Copy Document design pattern when they have requirements such as:

* To provide a quick and efficient way of moving content from a document to another.
* To allow reusing the document history as a template for new documents.
* To allow linking of documents that need to be applied to each other.

The Copy Document pattern involves the following entities:

1. Source document tables for document header and line. For example,Sales Header/Line.
2. Destination document tables for document header and line.

**Note:**The source document header/line and destination document header/line tables do not need to be the same. For example, you can copy a Sales Shipment Header/Lines into a Sales Header/Lines.
3. Copy Document engine: COD6620, Copy Document Mgt.
4. Copy Document report for a specific document type. The report requires the following parameters:
  * Source Document Type
  * Source Document No.
  * Include Header (optional)
  * Recalculate Lines (optional)
Example: REP901, Copy Assembly Document

[![ ][image0]][anchor0]

## Usage Sequence

**Precondition**: The user creates a new destination document Header, filling up the required information.

**Step 1**: The user runs the Copy Document report (element no. 4), filling up the parameters:

* Source Document Type
* Source Document No.
* Include Header and/or Recalculate Lines (not all Copy Document reports have these).

**Step 2**: The report copies the information in the source tables (Header and Line) into the destination tables (Header and Line).

**Post processing**: The user performs additional editing of the destination document.

The sequence flow of the pattern is described in the following diagram.

[![ ][image1]][anchor1]

Example: Copy Sales Document for Credit Memos.

In the standard version of Microsoft Dynamics NAV, the Copy Document functionality is implemented in the Sales Credit Memo window as shown in the following section.

\*\*\*

**Precondition**: The user enters data in PAGE44, Sales Credit Memo.

[![ ][image2]][anchor2]

**Step 1**: The user runs REP292, Copy Sales Document from the Sales Credit Memo window, populating the required parameters. The Include Header and Recalculate Lines fields are selected.

[![ ][image3]][anchor3]

**Step 2**: The Sales Credit Memo window is populated with information from the source sales document.

[![ ][image4]][anchor4]

**Post processing**: The user can now do additional editing of the sales credit memo.

# NAV Implementations

1. Copy Sales Document (REP292)
2. Copy Purchase Document (REP492)
3. Copy Service Document (REP5979)
4. Copy Assembly Document (REP901)

[watch?v=aTiwroXwW0&list=PLhZ3P LY7CqmVszuvtJLujFyHpsVN0Uw&index=17][anchor5]



[anchor0]: /cfs-file/__key/communityserver-wikis-components-files/00-00-00-00-42/clip_5F00_image002.gif
[anchor1]: /cfs-file/__key/communityserver-wikis-components-files/00-00-00-00-42/clip_5F00_image004.gif
[anchor2]: clip_5F00_image006.jpg
[anchor3]: clip_5F00_image008.jpg
[anchor4]: clip_5F00_image010.jpg
[anchor5]: https://www.youtube.com/watch?v=aTiwroXwW_0&list=PLhZ3P-LY7CqmVszuvtJLujFyHpsVN0U_w&index=17


[image0]: /resized-image/__size/750x0/__key/communityserver-wikis-components-files/00-00-00-00-42/clip_5F00_image002.gif
[image1]: /resized-image/__size/750x0/__key/communityserver-wikis-components-files/00-00-00-00-42/clip_5F00_image004.gif
[image2]: clip_5F00_image006.jpg
[image3]: clip_5F00_image008.jpg
[image4]: clip_5F00_image010.jpg
