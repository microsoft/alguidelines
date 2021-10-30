+++
title = "Singleton Table"
weight = 1110
+++
## Singleton Table

_By Elly Nkya at Microsoft Development Center Copenhagen_

## [![ ][image0]][anchor0]

_  
_

**Problem**: The developer needs to define a single record that can contain a set of rules and behavior (optional, mandatory, or defaulting mechanisms), that apply to a functionality, and can be configured by a user.

****

**Forces**

* You want a central place to define the address and logo of your company (see Company Information table).
* You want to define the no. series that should be used for your sales documents (see Sales & Receivables Setup table).
* You want to know whether your sales documents should be archived (see Sales & Receivables Setup table).
* You want to define the rounding accuracy your system should (see General Ledger Setup table).

**Solution:** Define a single record that can contain a set of rules and behavior (optional, mandatory, or defaulting mechanisms), that apply to a functionality, and can be configured by a user.

In a functionality that is large enough (such as sales, inventory, fixed) you may want to define a global set of rules, that are configurable by the user.

**Implementation**

**1\. Define: **Create a Setup Table with Dummy a Primary Key. Typically with type Code=10\. Then add fields to define the global rules.

**2\. Instantiate: **Place the instantiation code in a central place where it is guaranteed to be invoked before the functionality uses it. This is done in Codeunit 2\.

**3\. Enforce: **Give the user access to the record so that he can change the default setup, by creating a Card page. On the page, enforce the singleton to prevent deletion of the record or insertion of a new record

**4\. Use: **Access the rule in code and use it

****

**NAV Usages**

Rounding rules for Unit-Amounts and Amounts are implemented using the Singleton pattern.

**1\. Define:** The General Ledger Setup is used for this.

**2\. Instantiate:** In codeunit 2, the following code is invoked

    WITH GLSetup DO
    IF NOT FINDFIRST THEN BEGIN
    INIT;
    INSERT;
    END;

**3\. Enforce:** On the General Ledger Setup. The following properties are setup:

    DeleteAllowed=false,
    InsertAllowed=false

**4\. Use:** Access the rounding rules are used

    ...
    GLSetup.GET;
    UnitCostCurrency := ROUND(...,GLSetup."Unit-Amount Rounding Precision"); 
    ... 

Or if accessing the rule multiple times and performance is a consideration, use lazy instantiation:

    ...
    GetGLSetup;
    UnitCostCurrency := ROUND(...,GLSetup."Unit-Amount Rounding Precision");
    ...

    LOCAL GetGLSetup()
    IF NOT GLSetupRead THEN
    GLSetup.GET;
    GLSetupRead := TRUE;

**Related topics**

[Singleton design pattern][anchor1].

The **Singleton Table** has two established applications in Dynamics NAV:

1. [**Setup Tables**][anchor2] -- which are commonly storing user setup data in NAV,
2. **Cue Tables** -- used to calculate values for the visual representation of Cues on the NAV role center pages.

YouTube Video of NAV Singleton:

[watch?v=aQPu s9FkYI&list=PLhZ3P LY7CqmVszuvtJLujFyHpsVN0Uw&index=13][anchor3]



[anchor0]: 5554.Singleton-Table.png
[anchor1]: https://en.wikipedia.org/wiki/Singleton_pattern
[anchor2]: /nav/w/designpatterns/76.setup-table
[anchor3]: https://www.youtube.com/watch?v=aQPu-s9FkYI&list=PLhZ3P-LY7CqmVszuvtJLujFyHpsVN0U_w&index=13


[image0]: 5554.Singleton-Table.png
