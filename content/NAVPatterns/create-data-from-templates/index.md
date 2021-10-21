+++
title = "create-data-from-templates.md"
weight = 400
+++
_by Nikola Kukrika at Microsoft Development Center Copenhagen_

## Abstract

For many records, such as Items, Customers, and Vendors, users have to enter the same sets of data again and again. This is tedious, error-prone (users forget to enter a field or they choose the wrong group), and difficult to learn for some users.

We can group sets of data as templates to speed up and simplify the process of entering data in Microsoft Dynamics NAV. For example, the process of creating a new customer could be simplified so that users only have to enter information that is specific for every individual customer, e.g. Name and Address/Contact. 

## Description

This pattern solves the problem of creating new records based on their type. You should use it whenever there is a large set of data that could be grouped in a meaningful way.

In Microsoft Dynamics NAV 2013 R2, we have extended the Configuration Templates feature so that templates can be used in different languages than the language they were created in. We have also added the ability to set related templates so that related records can be inserted, such as dimensions for customers, items, and vendors.

The pattern consists of two parts:

1\. Using templates to create new records or applying templates to existing records

2\. Defining and updating existing templates 

## Usage

[![ ][image0]][anchor0]

Using the patterns involves three steps. 

1) As a first step, we must insert a record. This can be done either through C/AL code or by letting the user create a record using the **New** action. 

2) After the record is created, we must apply the template. This is done by using the **UpdateRecord** function in the **Config. Template Management **codeunit (8612). 

**Config. Template Lines** records reference one **Config. Template Header** record (lines pattern). The lines can be of type:

* **Field** - Stores a field value that will be applied to the record 
* **Related** Template -- References a Config. Template Header record for a related template. 

The **UpdateRecord** function applies values to the record one line at the time. One of the requirements was to be possible to use configuration templates in different language/regional settings than the template was created in.

To support this scenario, when applying the**Config. Template Line** record, **GLOBALLANGUAGE** is set to the language ID of the field. This is important because the default value is stored as text, so we need to use the same formatting that NAV was running on when the template was created. Otherwise, data types, such as Boolean, Date, etc., will raise validation errors. 

Any updates to a **Config. Template Line** record will automatically update the language ID to the current one. Since lines are applied one by one, it is supported to have lines with different language IDs belonging to the same template.

3) After we have applied the template to the record, we can insert related templates. For example, when you insert an item, you may want to insert dimensions as well. You must implement the logic to apply or then modify the related templates, since this depends on the logic and the relationship between the records. Lines with **Type = Related Template** are used to reference related templates.

Code example (Insert a record, apply a template, and insert the related templates): 

**// First insert a record Customer.INSERT(TRUE);**

**// Apply a template RecRef.GETTABLE(Customer);**

ConfigTemplateMgt.UpdateRecord(ConfigTemplateHeader,RecRef);

RecRef.SETTABLE(Customer);

**// Insert Dimensions -- related templates**

MiniDimensionsTemplate.InsertDimensionsFromTemplates(ConfigTemplateHeader,Customer."No.",DATABASE::Customer); 

Code to insert related templates (dimensions): 

**FUNCTION InsertDimensionsFromTemplates(ConfigTemplateHeader : Record "Config. Template Header";MasterRecordNo : Code\[20\];TableID : Integer)**

**// There are multiple records (multiple dimensions per master record)**

**// We have to set filter******

ConfigTemplateLine.SETRANGE(Type,ConfigTemplateLine.Type::"Related Template");****

ConfigTemplateLine.SETRANGE("Data Template Code",ConfigTemplateHeader.Code);****

****

IF ConfigTemplateLine.FINDSET THEN****

REPEAT****

ConfigTemplateHeader.GET(ConfigTemplateLine."Template Code");

**// Ensure that the table where the template belongs to is Dimensions**

**// We could have other related templates******

IF ConfigTemplateHeader."Table ID" = DATABASE::"Default Dimension" THEN****

InsertDimensionFromTemplate(ConfigTemplateHeader,MasterRecordNo,TableID);****

UNTIL ConfigTemplateLine.NEXT = 0;****

****

**// Create a new Dimensions Record and link it to the Master Record******

**FUNCTION InsertDimensionFromTemplate(ConfigTemplateHeader : Record "Config. Template Header";MasterRecordNo : Code\[20\];TableID : Integer)**

DefaultDimension.INIT;

DefaultDimension."No." := MasterRecordNo;****

DefaultDimension."Table ID" := TableID;****

DefaultDimension."Dimension Code" := GetDefaultDimensionCode(ConfigTemplateHeader);****

DefaultDimension.INSERT;****

****

RecRef.GETTABLE(DefaultDimension);****

ConfigTemplateMgt.UpdateRecord(ConfigTemplateHeader,RecRef);****

RecRef.SETTABLE(DefaultDimension); 

**To surface the action in the product, you have three options:**

1. **Recommended** - Implement a separate action called **New from Template**.
2. **Optional** - Implement the apply template function on the document itself. This is especially good in scenarios where users are allowed to change the template.
3. **Alternative** - Remove the new action by configuration or set Insert Allowed to FALSE on the list (this will block the creation of new records from the lookup). Implement an application action named **New** and tie it to your code.

**Note: ** In Microsoft Dynamics C5 2014, we chose to remove the **New** action with configuration since we wanted to promote the functionality and avoid the confusion in having too many options. However this might be difficult to maintain with a larger set of pages.

**To view or edit templates, you have two options:**

1. Use the **Config. Template List** table (8620) and the **Config. Template Header Card **table (8618).

This is a generic solution that is not very usable and is error-prone (no lookups, checks for length, table relation checks, etc.) The default value is a text field of 250 characters, which might be much more than the field length, and may lead to validation errors when used. Users will most likely not be able to use this page.

[![ ][image1]][anchor1]

2\. Implement custom pages resembling the document.

This is optional if you want to enable the users to create and modify templates. In C5 2014, we created temporary tables with the same fields as the main record. Based on this temporary record, we built a page that resembles a document.

Example of the **Customer Template** page:

[![ ][image2]][anchor2]

The goals of this solution were:

* To make the setup page resemble a document page so that it is easy to use with basic validation and lookups.
* To have only one place to store templates and maintain only one business logic for applying them, namely in the **Configuration Template Header** table.
* To avoid any lateral effects of doing validation on the temporary master record. Doing validation on fields, even though the record itself is temporary, could permanently modify other data in the database. For example, if you insert a new record in the **Customer** table, even in a temporary table, a Contact record is created, which will not be temporary.
* Testability: It is easy to test through RecordRef that the template table matches the main table. We can compare field lengths, data types, table relations, etc. The test is able to detect that they are out of sync, so it is easy to prevent errors.

One example in the product is the **Mini Customer Template **table (1300).

The table itself contains very little code. OnModify, OnInsert, and OnDelete triggers update the **Configuration Header** and **Configuration Lines** tables. The following functions in the **Config. Template Management **codeunit (8612), are used for this:

* ConfigTemplateManagement.CreateConfigTemplateAndLines
* ConfigTemplateManagement.UpdateConfigTemplateAndLines
* ConfigTemplateManagement.DeleteRelatedTemplates

The CreateFieldRefArray function is used as an interface function on all the temporary template tables. It builds data to be read/written to the configuration templates.

To further enhance the usability, we have provided the following additional functionality:

* Create a template from the existing record: The user opens an existing record and creates a template from that record. All the fields that are defined in the CreateFieldRefArray function are used to create the new template.
* Templates list: This page is used by users to select templates or create new ones. Depending on which templates they are working on, we show different template cards.

## NAV Specific Example

In C5 2014, this is the workflow:

The user opens the **Customers List** window and selects **New**

**[![ ][image3]][anchor3]**

**[][anchor3]**From this page, the user can view the template, edit it, or create a new one. Selecting a template will populate the customer card and open a new record. From the existing record, the user has options to save as a template or opening a list of templates to maintain available templates. Selecting a template will populate the customer card and open a new record. From the existing record, the user has options to save as a template or opening a list of templates to maintain available templates.

[![ ][image4]][anchor4]

From the **Customer Card Template **window, we can invoke the **Dimensions** action, through which we can define the dimensions that will be inserted together with the template:

[![ ][image5]][anchor5]

## NAV Usages

This pattern is used in Microsoft Dynamics C5 2014 in the following objects:

* Temporary template tables:
  * **Mini Customer Template **table (1300)
  * **Mini Item Template **table (1301)
  * **Mini Dimensions Template** table(1302)
  * **Mini Vendor Template** table (1303)

* Pages to define templates:
  * **Mini Customer Template Card** page (1341)
  * **Mini Item Template Card **page (,1342)
  * **Mini Dimensions Template List** page (1343)
  * **Mini Vendor Template Card** page (1344)

* Pages that use the templates:
  * **Mini Customer List** page (1301)
  * **Mini Item List** page ( 1303)
  * **Mini Vendor List** page (1331)

In the standard version of Microsoft Dynamics NAV, we use the **Apply Template** action on the following pages:

* **Customer Card** page (21)
* **Vendor Card** page (26)
* **Item Card** page (30)
* **Resource Card** page (, 76)
* Other similar cards.

## Ideas for improvement

Implement the solution in the standard version of Microsoft Dynamics NAV and extend the Apply Template functionality to insert dimensions. 

It is possible that users end up with a large number of templates if they need many different data combinations. An improvement could be to split templates into smaller groups, grouping only part of the fields that are related, and then apply only these. 

[watch?v=F0CTvoyKSmI&list=PLhZ3P LY7CqmVszuvtJLujFyHpsVN0Uw&index=20][anchor6]



[anchor0]: 4118.Picture1.png
[anchor1]: 2816.Picture3.png
[anchor2]: 7271.Picture4.png
[anchor3]: 4341.Picture4.png
[anchor4]: 3482.Picture-5.png
[anchor5]: 2134.Picture6.png
[anchor6]: https://www.youtube.com/watch?v=F0CTvoyKSmI&list=PLhZ3P-LY7CqmVszuvtJLujFyHpsVN0U_w&index=20


[image0]: 4118.Picture1.png
[image1]: 2816.Picture3.png
[image2]: 7271.Picture4.png
[image3]: 4341.Picture4.png
[image4]: 3482.Picture-5.png
[image5]: 2134.Picture6.png
