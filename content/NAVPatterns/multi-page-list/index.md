+++
title = "multi-page-list.md"
weight = 810
+++
_By Bogdana Botez at Microsoft Development Center Copenhagen_

__

### **Abstract**

This pattern describes how to open a related document (or card) page from a list page, for the case when there can be more than one pages associated to the rows of the list page.

### **Description**

The example below illustrates the connection of a List Page with multiple Document Pages, while the second example links the List Page with Card Pages.

[![ ][image0]][anchor0]

The records contained in a list page have an associated page, which is specified in the CardPageID property of the list page. The NAV infrastructure assures the following scenarios are in place, without the need to add any explicit C/AL code:

1. From the selected record in the list page, the user can double-click in order to open the related card page.
2. The "Edit" action is available on the ribbon as well as in the right-click context menu of the list page rows. Invoking this action, opens the related card page.

However, there are situations when rows of the list page can correspond to different pages each (either cards or documents). For example, consider a list page containing 3 rows, requiring the following behaviour:

Row1

Opens page ID 1

Row2

Opens page ID 2

Row3

Opens page ID 3

This situation is not handled automatically by NAV. There is no possibility to specify more than one CardPageID in the list page properties. Furthermore, there is no possibility to specify one or more document page IDs on the list page. Therefore, those cases need to be handled explicitly by the C/AL developer.

### **Usage**

The solution used in NAV implementations is at the list page level, as following:

1. The property CardPageID of the list page remains undefined.
2. An action named "Show Document" or "Card" is created on the Navigate tab, with the properties:

* Image = EditLines
* Promoted = Yes
* ShortCutKey = Shift+F7

1. The OnAction trigger for the Card action, contains explicit logic to run the targeted card page. It can, for example, be a CASE statement, which invokes PAGE.RUN(...) based on an enumeration field of the current row.

### **NAV Specific Example**

For example, the NAV page Sales List (page ID 45), which displays the Sales Header Table (table ID 36), chooses which card to open, based on the Document Type field. This is an option field, which can have the following values: Quote, Order, Invoice, Credit Memo, Blanket Order, Return Order. For each document type, the related card page must be opened.

[![ ][image1]][anchor1]

For this purpose, a new action ("Card") is added to the Sales List page. The OnAction trigger of this new action contains the page selection logic:

CASE "Document Type" OF

"Document Type"::Quote:

PAGE.RUN(PAGE::"Sales Quote",Rec);

"Document Type"::Order:

PAGE.RUN(PAGE::"Sales Order",Rec);

"Document Type"::Invoice:

PAGE.RUN(PAGE::"Sales Invoice",Rec);

"Document Type"::"Return Order":

PAGE.RUN(PAGE::"Sales Return Order",Rec);

"Document Type"::"Credit Memo":

PAGE.RUN(PAGE::"Sales Credit Memo",Rec);

"Document Type"::"Blanket Order":[  
][anchor2]

PAGE.RUN(PAGE::"Blanket Sales Order",Rec);

END;

### **NAV Usages**[  
][anchor3]

Some of the NAV implementations of this pattern can be found in the following pages:

1. Sales List (45) and Sales List Archive (5159)
2. Purchase List (53) and Purchase List Archive (5166)
3. Available - Sales Lines (499)
4. Sales Lines (516)
5. Purchase Lines (518)



[anchor0]: Multi-page-list-img-1.jpg
[anchor1]: Multi-page-list-img-2.jpg
[anchor2]: http://sharepointemea/sites/DynamicsNAV/Wiki/Nav%20Wiki%20Documents/NAV%20App%20Patterns/NAV%20App%20Patterns%20for%20Review/Multi-Page%20List.docx#_msocom_5
[anchor3]: http://sharepointemea/sites/DynamicsNAV/Wiki/Nav%20Wiki%20Documents/NAV%20App%20Patterns/NAV%20App%20Patterns%20for%20Review/Multi-Page%20List.docx#_msocom_7


[image0]: Multi-page-list-img-1.jpg
[image1]: Multi-page-list-img-2.jpg
