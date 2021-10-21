+++
title = "multilanguage-application-data.md"
weight = 820
+++
_by Bogdana Botez at Microsoft Development Center Copenhagen_

## Abstract

Generally, NAV translation refers to the translation of UI elements like captions and user texts (messages on dialogs, warnings, error messages). This translation is done by the Microsoft Dynamics NAV team before releasing the localized version of the product.

But there is one more scenario. In this scenario, Cronus International Ltd., wants to sell a "Fiets" to a Dutch customer, a"Cykel" to a Danish one, and a "Bicicletta" to an Italian customer. All 3 are the same inventory item - and its default name is "Bicycle". But for reporting, Cronus International Ltd. wants to use the customer language preferences for translating the bicycle's name.

Sometimes there's a need to support multiple languages for domestic transactions, too. For example, [Switzerland has 4 official languages][anchor0]: German, French, Italian and Romansh, the first 3 of them being supported by NAV.

## Description

The example below uses the Item Translation feature of NAV, however, implementations of the same pattern exist for other application areas.

### How to use the pattern

**Enter translations for "Bicycle"**

In the Windows client, on the bicycle Item card, on the Home ribbon tab, choose Translations.

[![ ][image0]][anchor1]

On the opened page, enter the Danish (language code DAN), the Italian (ITA), and the Dutch (NLD) translations for "Bicycle".

[![ ][image1]][anchor2]

**Set the desired language for the Dutch, Danish, and Italian customers**

On the customer card for your 3 customers, in the Foreign Trade FastTab, choose the preferred language for each customer. If no language is specified, then the default item description will be used for items sold or anyhow associated to that customer. If for example, the DAN (Danish) language is specified for the customer, and the "Bicycle" has a translation in Danish, then this translation "Cykel" will be used instead of the default name "Bicycle".

[![ ][image2]][anchor3]

**See the result**

After those changes, when the customer (in this case the Danish "Lauritzen Kontormøbler A/S") transactions a bicycle, the translated description "Cykel" will be displayed on the documents and reports. For example, creating a sales order for this customer with 1 item No. 1000, shows:

[![ ][image3]][anchor4]

## Usage

**Create the translation table**

Named "<Entity\> Translation" table, where <Entity\> is replaced with the name of the actual object being translated. For the Item example above, this table will be named "Item Translation".

The table definition contains at a minimum:
**Field**
**Description**
Entity ID field
For example, Item No.
Language Code
Identifies the language of this translation string (for example, "DAN" (Danish), "BGR"(Bulgarian). This is one of the language codes defined in the Languages table.
Translation
The translated string.

The table above has a key composed of the first two fields.

**Create the user interface for entering new translations of <Entity\>**

* Create a Translations page to present the table created above
* On the Entity card - add a Translations menu option which will open the Translations page

## NAV Usages

Some of the NAV implementations of this pattern are:

1. Item Translation
2. Payment Term Translation
3. Shipment Method Translation
4. Unit of Measure Translation

## Related Topics

The **Extended Text** pattern is a more powerful version of the **Multilanguage application data **pattern presented in this section. The main differences are: **Pattern**
**Multilanguage Application Data**
**Extended Text**
**Supports translation of application data**
Yes
Yes
**Format**
Single- or multi-line
Single-line
**Applies to document type**
Can choose which document types are affected.
All document types are affected.



[anchor0]: http://en.wikipedia.org/wiki/Languages_of_Switzerland "Switzerland has 4 official languages"
[anchor1]: 5670.1.png
[anchor2]: 6746.2.png
[anchor3]: 2746.3.png
[anchor4]: 4812.4.png


[image0]: 5670.1.png
[image1]: 6746.2.png
[image2]: 2746.3.png
[image3]: 4812.4.png
