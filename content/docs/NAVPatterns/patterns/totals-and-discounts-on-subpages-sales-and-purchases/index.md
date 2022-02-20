+++
title = "Totals and Discounts on Subpages Sales and Purchases"
weight = 1220
+++
_By Nikola Kukrika at Microsoft Development Center Copenhagen_

## Abstract

To increase discoverability and productivity, critical information, such as statistics, can be moved from separate pages onto the document where it is needed.

For example, documents should clearly display totals and discounts information to provide quick overview, make it easy to relate the amounts to the document currency, and to allow the user to see and apply discounts directly on the document.

## Description

Before Microsoft Dynamics NAV 2015, totals and discount information was scattered between the document and the Statistics page, which made it less discover-able and less usable for new users. It was difficult to see the discount amounts, relate the amounts to the currency of the invoice, and it was not-intuitive that you had to update the Statistics page to see updates on the document. In addition, the result of the update was not visible on the main document, which added to the confusion.

Totals and discount information logically belongs on the document, as is the case on many competitor products.

The Statistics page contains too much information for most common scenarios, and only a part of the information is visible on the related document. See the following example for sales order statistics.

[![ ][image0]][anchor0]

With the pattern implemented, selected statistics fields are placed on the sales order, as in the following example.

[![ ][image1]][anchor1]

This new layout gives precise and complete information about totals and discounts, with discounts on the left side and totals on the right side. The currency is clearly shown, and the layout resembles a printed document. The values are always updated (with some exceptions that are explained in the next following sections), and the user can apply a discount directly on the document.

Options considered when deciding on the layout:

* A new FactBox
* A new FastTab
* On the Lines subpage

The Lines subpage option was chosen because:

* Can be made with cleaner code
* Is always visible, not collapsible, as opposed to FactBox and FastTab options
* Less chrome in the UI

## Usages

Concerning update of totals and discounts, the following were considered:

* All code should be in one place
* Existing VAT, invoice discount calculation, etc. should be reused and not re-implemented.
* The presentation layer should be separated from the logic.
* Presentation layer should have simple (one liner) calls to the logic
* System responsiveness should not be significantly affected
* Totals and discounts should be updated "on-the-fly" on any change that affects amounts

The following artifacts were implemented to achieve the desired functionality.

### Flags That Totals Are Not Updated

In order to always update the totals, the system must have a way to mark that a specific change should trigger a recalculation of the totals. To achieve this, the following was implemented.

* On the lines:
  * A new field, Recalculate Invoice Discount, was added and set to FALSE by default.

The _UpdateAmounts_ function sets the new field to TRUE for any execution. This function was chosen as it is a central entry point for any amount-related change.

[![ ][image2]][anchor2]  

* On the header:
  * A new FlowField, Recalculate Invoice Discount was added. This field is TRUE if at least one line (that relates to the header) has been marked as "dirty", meaning that totals must be recalculated.

[![ ][image3]][anchor3]

Update diagram

[![ ][image4]][anchor4]

### Total Calculation and Update Logic

The following new codeunits were added:

* COD56 and COD66 (one for sales and one for purchase) are responsible for the "heavy" tasks of calculating/applying totals and discounts by calling into existing code, similarly to how the Statistics page works.
* After recalculation, the flag field is reset on all the lines related to a specific header.
* These calculations, in the existing implementation, are triggered ONLY in case of UI scenarios. Non-UI scenarios are not affected.

**Note:** In case of web-service implementations that do not use the existing pages, the total calculation and the invoice discount logic should be called specifically to display similar functionality.

### UI-Related Updates

A new CU, COD57 has been added and it contains the logic used to update the fields that will, in the end, be displayed in the UI. Decision was to merge both the sales and purchase functionality into one place.

### Update Controls

The pattern is applied to both non-posted documents (orders, invoices, etc.) and posted documents (posted Invoice, etc.). Because the posted document is a document that does not change, the implementation is simplified as follows.

#### Subpage - Non-posted documents

The _OnAfterGetCurrRecord_ function call is responsible for updating the totals- and discount-related controls.

To avoid duplicating lots of code in many places, a single function with more parameters "pattern" was chosen. The function returns all the controls updated in a single call, as follows.

[![ ][image5]][anchor5]

Manual invoice discount. One function call when applying a manual invoice discount, as follows.

[![ ][image6]][anchor6]

**Note:** After applying the manual discount, the lines should be refreshed as they will be changed in batch mode.

The following "decorators" were added to recalculate amounts on the fly:

* Totals should always be updated as soon as the user leaves the field.

Code should be added to the validate triggers of the controls that are changing the amounts in order to refresh the total controls.[![ ][image7]][anchor7]

**Note:** Amounts will not always be up to date because of the following:

* Performance. The update must be triggered manually if the number of lines is above 100\. (100 was chosen because performance measurements have demonstrated that with more than 100 lines, the performance was worse than 0.5-1s in update time.)
* Missed/customized triggers or header changes - cannot trigger lines update from header. Likewise, lines cannot trigger the header to update.
* In the above cases, a link next to the totals will be displayed informing user that totals/discounts might not be updated and they can be updated by selecting the link

#### Subpage -- Posted Documents

Only the _OnAfterGetCurrRecord _function will have code because posted documents cannot be changed. Therefore, totals do not need to be updated.

[![ ][image8]][anchor8]

### UI -- Presentation Layer:

After Platform added support for this specific case, the layout is very simple, as follows.

[![ ][image9]][anchor9]

This will produce the layout we wanted:

[![ ][image12]][anchor13]

### UI -- Manual Totals Update

As mentioned before, there are cases when the user is informed that totals/discounts might not be up to date and that it should select a link in order to get the updated values.

The link is part of the sub-part where totals and discounts are added. The visibility is controlled by actually controlling the text (message, or empty), because Platform does not support making only one visible/invisible (it must be a group). When the link is displayed, the styling is also changed for the total/discount controls, and the values are set to 0\.

[![ ][image13]][anchor14]

## NAV Usages

We have implemented these scenarios for sales and purchase documents (order, invoice, quote, credit memos, return orders, including posted documents). Note that the implementation in NAV 2015 is a bit different than the one in NAV 2014 C5 objects (1300 range). It was decided not to merge the two implementations, among other reasons because the NAV 2014 C5 implementation does not fit the full application.

The pattern can easily be extended to new sales and purchase documents. If the pattern is followed, it can also be extended to other types of documents, such as in services, jobs, etc.

**Note:** This new pattern is not implemented in the North America and India versions. This is due to special local features in the two versions regions and the multitude of sales tax/vat options that would make the solution non-user friendly and not optimized for performance.

## Ideas for Improvement

Platform support to be able to trigger full-page update from the lines. Support does exist today, but it is not programmatically controlled at design time. This means that it cannot be used in all cases.

{{< youtube U3noU-WT8Xk>}}



[anchor0]: 4300.Fig-1.jpg
[anchor1]: Fig-2.jpg
[anchor2]: Fig-3.jpg
[anchor3]: 6305.Fig-4.jpg
[anchor4]: Fig-5.jpg
[anchor5]: Fig-6.jpg
[anchor6]: Fig-7.jpg
[anchor7]: Fig-8.jpg
[anchor8]: Fig-9.jpg
[anchor9]: Fig-10.jpg
[anchor10]: /cfs-file.ashx/__key/communityserver-wikis-components-files/00-00-00-00-42/Fig-11.jpg
[anchor11]: /cfs-file.ashx/__key/communityserver-wikis-components-files/00-00-00-00-42/Fig-12.jpg
[anchor12]: https://www.youtube.com/watch?v=U3noU-WT8Xk&list=PLhZ3P-LY7CqmVszuvtJLujFyHpsVN0U_w&index=22
[anchor13]: Fig-11.jpg
[anchor14]: Fig-12.jpg


[image0]: 4300.Fig-1.jpg
[image1]: Fig-2.jpg
[image2]: Fig-3.jpg
[image3]: 6305.Fig-4.jpg
[image4]: Fig-5.jpg
[image5]: Fig-6.jpg
[image6]: Fig-7.jpg
[image7]: Fig-8.jpg
[image8]: Fig-9.jpg
[image9]: Fig-10.jpg
[image10]: /resized-image.ashx/__size/550x0/__key/communityserver-wikis-components-files/00-00-00-00-42/Fig-11.jpg
[image11]: /resized-image.ashx/__size/550x0/__key/communityserver-wikis-components-files/00-00-00-00-42/Fig-12.jpg
[image12]: Fig-11.jpg
[image13]: Fig-12.jpg
