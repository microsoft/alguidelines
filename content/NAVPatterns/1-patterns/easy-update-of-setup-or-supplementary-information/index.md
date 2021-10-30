+++
title = "Easy Update Of Setup Or Supplementary Information"
weight = 520
+++
_by Anders Larsen at Microsoft Development Center Copenhagen_

## Abstract

Users or the administrator must regularly update setup or supplementary information in the day-to-day business, such as setting up a new type of customer. This setup task often arrives when their focus is on registration and execution instead of setup.

The navigation experience around these extra steps is often quite troublesome and time-consuming. To enable users to easily perform the needed update, a guide often gives the best support.

To guide users, we can prompt them with a dialog on which they can update the setup or supplementary information instantly and easily, so that they can proceed with the business task without being side-tracked. 

## Usage

Define two functions in the setup or supplementary table: One for verifying if the needed information is available, and another for exposing the page that contains the fields that the user must update.

Call the code. For example:

Local IsXAvailable : Boolean   
If field X <\> '' then   
Exit(True)  
Exit(false)  
  
VerifyAndSetX  
If IsXAvailable then  
Exit;  
If Confirm('Field X is missing a value. Do you want to update it now?') then  
Open the card page in edit mode  
  
If not IsXAvailable then  
Error(Field X is missing a value. Please correct it.)

The calling code

..

SetupTable.VerifyAndSetX

..

## NAV Specific Example

****

In the **Sales & Receivables Setup** table (311) for the DK version, the following procedures have been added:

Local Procedure IsOIOUBLPathAvailable(...)

Procedure VerifyAndSetOIOUBLPath(...)

The code in the **Sales & Receivables Setup** table can now be called directly from the related processing codeunit, such as the **Sales-Post + Print** codeunit (82).

Were the code is called:

IF ("EAN No." <\> '') THEN

SalesSetup.VerifyAndSetOIOUBLPathSetup(SalesHeader."Document Type");

If the setup is not updated properly, the user is prompted to update it as follows.

[![ ][image0]][anchor0]

Choosing **Yes** opens the related setup page.

[![ ][image1]][anchor1]

## NAV Usages

* Report 206, **Sales invoice**
* Table 79, **Company Information**
* In OIOUBL fields (DK version) during posting/printing of a sales invoice.

## Ideas for Improvement

Make a more generic platform implementation that launches the corresponding card page for Rec on Rec.testfield with an asterisk mark for the field that needs a proper value.

## Related Topics

The anti-pattern is to do a testfield on a field that is not in the table that you are currently updating.

The test field message can often be confusing because the pages are often named differently than the tables, which can lead to misunderstanding and context-switching.

[watch?v=oeASJN zqTo&list=PLhZ3P LY7CqmVszuvtJLujFyHpsVN0Uw&index=18][anchor2]



[anchor0]: 0654.easy-update-1.png
[anchor1]: 4024.easy-update-2.png
[anchor2]: https://www.youtube.com/watch?v=oeASJN-zqTo&list=PLhZ3P-LY7CqmVszuvtJLujFyHpsVN0U_w&index=18


[image0]: 0654.easy-update-1.png
[image1]: 4024.easy-update-2.png
