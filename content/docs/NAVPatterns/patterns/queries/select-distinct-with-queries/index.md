+++
title = "SELECT DISTINCT with Queries"
weight = 1040
tags = ["C/AL"]
categories = ["Pattern"]
+++
_By Bogdana Botez, at Microsoft Development Center Copenhagen_

### Abstract

This pattern explains how to perform SELECT DISTINCT by using queries in Dynamics NAV.

**Description**

When working with tables, sometimes a developer needs to perform a SELECT DISTINCT (also known as SELECT UNIQUE) from a table. As NAV does not provide this out of the box, we present below a way to select unique records by using queries.

**Problem statement**

Let's consider the **VAT Entry** table as below: [  ][anchor0]

[![ ][image0]][anchor1]

The goal is to select one line for each separate document that produced VAT Entries. In other words, we want records grouped by **Type, Document Type** and **Document No.**. However, if there are multiple lines with the same value of the triad **Type, Document Type** and **Document No.** in the **VAT Entry** table, we only want to see one of them.

#### Solution

Create a new query object **VAT Entry Distinct Document No.**, with a single DataItem sourced from **VAT Entry** table. Add the three desired group-by fields **Type, Document Type** and **Document No.** as columns.

[![ ][image1]][anchor2] 

To enable grouping, add one more column, with **Method Type** = **Totals**. This will automatically set the **Group By** checkbox to TRUE on the three precedent fields.

Note that the **Group By** field is read-only and trying to set it by hand will clarify that:

[![ ][image2]][anchor3]

Running the query yields a single record per document. You can notice in the second line below for example, how the sales invoice number 103001 had 2 VAT Entries, but it shows up only once in the query:

[![ ][image3]][anchor4] 

### Example

One thing is to be noted: there is a limitation to how much information you can take out from the records. For example, if we need to extract more information than just the one we already have in the columns, then the following apply: adding one more column of **Method Type** = **None** will indeed show more information, but it _might_ affect the grouping. More details below.

**The grouping is affected** for example when the additional column is the **VAT Entry No**. In this case, this column brings up additional grouping criteria and one group for each entry number will end up being created.

[![ ][image4]][anchor5]

You can notice that both lines of document 103001 are not visible, which contradicts the goal of SELECT DISTINCT.

[![ ][image5]][anchor6] 

**The grouping is not affected** when the additional column does not influence grouping (has variation identical with one of the other existing columns). For example, adding **Posting Date** as a new column, does not change anything because posting date is the same for all lines of a document, so the query result is identical with the initial one:

[![ ][image6]][anchor7] 

Below is the result of this query, where you can notice that the initial grouping is preserved and correct. Bonus, we can now read the **Posting Date** of the document too.

[![ ][image7]][anchor8] 

**The grouping is also not affected** when adding more columns of **Method Type** = **Totals**. However, this can only be done with columns of Decimal data type.

### **NAV Usages**

This pattern is used in Query 19: **VAT Entries Base Amt. Sum**. This query is used in Report 19: **VAT- VIES Declaration Tax Auth**.

[![ ][image8]][anchor9] 

The pattern is also used in Query 1511: **User IDs by Notification Type**.  

  
### **Related Topics**

A variation using loops could be described, for C/AL development on NAV 6 where query objects are not available.



[anchor0]: https://microsoft.sharepoint.com/teams/DynamicsNAV/Wiki/Nav%20Wiki%20Documents/NAV%20App%20Patterns/NAV%20App%20Patterns%20for%20Review/Table%20Select%20Distinct.docx#_msocom_1
[anchor1]: 6521.clip_5F00_image001.png
[anchor2]: clip_5F00_image002.png
[anchor3]: clip_5F00_image003.png
[anchor4]: clip_5F00_image004.png
[anchor5]: clip_5F00_image005.png
[anchor6]: clip_5F00_image006.png
[anchor7]: clip_5F00_image007.png
[anchor8]: clip_5F00_image008.png
[anchor9]: Untitled-picture.png


[image0]: 6521.clip_5F00_image001.png
[image1]: clip_5F00_image002.png
[image2]: clip_5F00_image003.png
[image3]: clip_5F00_image004.png
[image4]: clip_5F00_image005.png
[image5]: clip_5F00_image006.png
[image6]: clip_5F00_image007.png
[image7]: clip_5F00_image008.png
[image8]: Untitled-picture.png
