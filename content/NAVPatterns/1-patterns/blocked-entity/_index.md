+++
title = "Blocked Entity"
weight = 270
+++
_Originally by Abhishek Ghosh at Microsoft Development Center Copenhagen_ 

## Abstract

The Blocked Entity is used when it is required to stop transactions for an entity (mostly master data), temporarily or permanently.

[![ ][image0]][anchor0]

## Description

To block entities through metadata, read this pattern. To do the same thing through data, read [Data Driven Blocked Entity pattern][anchor1].  

The business entity holds a state that controls if a given transaction is allowed. The state is used by the logic controlling transactions.  The change of state could either be temporary or permanent.

An example of a temporary halt is when a retail chain selling items has received lot of complaints about an item, and the company wants to stop all transactions, both purchase and sale, with that item until the dealer has clarified the issue with his supplier and possibly received a replacement for the defective stock. Another common example is during counting the physical inventory using cycle counting where the counting is done in one section of a warehouse at a time, so that the regular operations can continue in the other parts of the warehouse. In these situations, it is necessary to block all transactions, such as picks and put-aways, for a bin while warehouse counting is in progress for that bin.

In contrast, a permanent halt to transactions could be required when an item has become obsolete (or is about to become obsolete), and the company wants to stop further purchase or sale of the item. However, the company wants to maintain the transaction history of the item and, therefore, does not want to delete the item record.

A simple design implementation of such requirements in Microsoft Dynamics NAV is to add a Blocked field in the entity table (and on the associated page). The implementation takes this state into the logic and checks for the value of this field in related transactions. For most simple scenarios, it is sufficient to have two states on the Blocked field, specifying whether it is allowed to perform transactions for the entity or not.

In certain situations, however, there could be different levels of blocking. For example, the company could block all sales to a customer that has overdue payments, and the company does not want to allow transactions with this customer until the payments are received. In other situations, the customer may have raised objections about an invoice, and the company has decided not to generate new invoices for the customer until the issue has been resolved. However, the company does want to continue shipping goods to the customer so as not to impact the customer's operations. In these scenarios, it may be necessary to have multiple states on the Blocked field depending on the level of restriction that is needed.

## Usage

As mentioned in the previous section, there are two implementations depending on business requirements: The 2-state Boolean field for simple implementations and the multi-state option field for more complex requirements. The implementation flow is similar for both patterns, except how the validation is implemented. The following discusses the two scenarios one by one.

### Boolean Implementation

Add a Boolean field named Blocked in the table.

In the relevant logic, add a condition to check the status of the Blocked flag. The cheapest way is to use a TESTFIELD:

```AL
<rec variable\>.TESTFIELD(Blocked,FALSE);
```

Alternatively, you can throw a custom error message. However, you should only do that if the default error message thrown by TESTFIELD is not sufficient.

### Option-Field Implementation

Add an option field named Blocked in the table. The option values will reflect the different blocked states required by the company.

Add this field on the card page (or on the List page if the entity does not have a card). As with the Boolean implementation, the convention is to add this field in the right-hand column in the General FastTab of the card page.

Implement a function in the table that takes the transaction context as input and evaluates the Blocked field to decide whether the transaction should be allowed or not. Optionally, the function can be responsible for notifying the user and bubble up an error message straight away.

Note: the option field assumes that only one of the multiple options can be active at a time. In other words, the options should be mutually exclusive. 

How not to use the option field in this case: if we want to block an item from sale and/or purchase, the 4 combined options would be **Block none** | **Block Sales** | **Block Purchases** | **Block Sales and Purchases**. This doesn't scale, because if now we need to block another transaction, the number of option would grow too fast. In this situations, it is better to use two Boolean fields: **Blocked Sale**: **true|false** and **Blocked Purchase: true|false**.

A good example of usage would be for varying the behavior depending on the chosen option, for example by displaying a different error message depending on the reason an Item is blocked. In this case we can have the item **Not Blocked** | **Blocked due to defect** | **Blocked waiting for approval**, etc. 

## NAV Specific Example

### Boolean Implementation

[![ ][image1]][anchor2]

An example of the Boolean implementation on the Item card.

In codeunit 22 -- Item Jnl.-Post Line, the following lines of code have implemented a check based on the value of the Blocked field:

```AL
IF NOT CalledFromAdjustment THEN
    Item.TESTFIELD(Blocked,FALSE);
```
### Option-Field Implementation

[![ ][image2]][anchor3]

An example of the option field implementation on the Customer card.

The CheckBlockedCustOnDocs and CheckBlockedCustOnJnls functions in the Customer table are responsible for validating the Blocked state with respect to the input document type. These functions are invoked in several areas, such as posting routines, where a status check on the Blocked field is required. This is a good practice where the Blocked implementation gets more complex, as this encourages reuse and ensures uniformity of implementation.

## NAV Usages

Entities where the Blocked Entity has been implemented include:

* Item
* G/L Account
* Customer
* Vendor
* Bin  

## Related Topics

The [Released Entity][anchor4].

{{< youtube O2R-fTSup1o >}}

[anchor0]: 2260.BlockedEntityPattern.png
[anchor1]: /nav/w/designpatterns/247.data-driven-blocked-entity/edit
[anchor2]: 8637.BlockedEntityPattern_5F00_5F00_5F00_Boolean.png
[anchor3]: 3056.BlockedEntityPattern_5F00_5F00_5F00_Option.png
[anchor4]: /nav/w/designpatterns/115.released-entity.aspx


[image0]: 2260.BlockedEntityPattern.png
[image1]: 8637.BlockedEntityPattern_5F00_5F00_5F00_Boolean.png
[image2]: 3056.BlockedEntityPattern_5F00_5F00_5F00_Option.png
