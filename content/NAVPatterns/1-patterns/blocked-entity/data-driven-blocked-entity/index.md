+++
title = "Data Driven Blocked Entity"
weight = 470
+++
_Written by Bogdan Andrei Sturzoiu, at Microsoft Development Center Copenhagen_

**Abstract**

This pattern implements a generic mechanism for dynamically restricting and allowing usage of a record by the business process administrator. 

**Problem**

A NAV record can be used in a number of functionalities across the app. There are situations, however, when the administrator wants to restrict the consumption of such a record, as well as lift the restriction when it is no longer relevant.

For example, a new customer record should not be used for posting documents until it is approved by the relevant approver.

We could solve this by using the [Blocked Entity pattern][anchor1], but it requires database schema changes, which have an upgrade impact.

The blocked entity pattern involves:

1. Adding a "blocked" status field on the record (either a Boolean or in the more advanced cases, an option field refining the usage).
2. Adding specific code for the record in every place where the restriction needs to be enforced.

In contrast, the Data-driven Blocked Entity pattern involves adding a new record (data change) to mark the restriction, instead of adding a new field (metadata change).

**Solution**

This pattern describes a generic mechanism of adding and lifting restrictions for any type of record.

The restriction mechanism has the following elements:

1. Adding a restriction record for a specific reason (e.g. the record requires approval), which will act as a surrogate key (unique identifier) for the restricted record. This can be implemented through a workflow response, or directly, by calling the Restriction Management codeunit function.
2. Lifting the restriction when it is no longer necessary. Again, this can be done using a workflow response or directly by calling the dedicated function.
3. Consuming the restriction in the places of interest for a specific purpose. This is an application feature that requires a call to the Restriction Management codeunit to check for restrictions.

Currently, the restrictions are record-based and type-less. They act as simple tokens, and they have:

* A reason (e.g. the record requires approval)
* A purpose (e.g. the record cannot be posted).

You must make sure to differentiate between the reason and the purpose. That is because the restriction can only be added once per record, but consumed in multiple places.

****

**Example**

For example, we want to restrict posting Gen. Journal Lines if a customer has not been added in Account No. field.

For this, the following components are needed:

1. When a Gen. Journal Line is inserted, call RestrictRecordUsage in COD1550, either directly in the trigger or using an event subscriber.
2. When you validate a Customer No. as Account no. and Customer as Account Type, lift the restrictions by calling AllowRecordUsage in COD1550\.
3. The consumption of the restriction at posting is already implemented as an event in TAB81, OnCheckGenJournalLinePostRestrictions. No further action necessary.

**NAV Usage**

All the approval workflows include a response that restricts usage of a record, and then, at the end of an approval loop, a response that allows the usage again by lifting the restriction. See responses "Add record restriction" and "Remove record restriction" implemented in COD1521\.[  
][anchor2]

The code behind the "Add record restriction" workflow response:

    RecRef.GETTABLE(Variant);
    Workflow.GET(WorkflowStepInstance."Workflow Code");
    RecordRestrictionMgt.RestrictRecordUsage(RecRef.RECORDID,STRSUBSTNO(RestrictUsageDetailsTxt,Workflow.Code,Workflow.Description));
    

The code behind the "Remove record restriction" response:

```AL
RecRef.GETTABLE(Variant);
CASE RecRef.NUMBER OF
    DATABASE::"Approval Entry":
        BEGIN
            RecordRestrictionMgt.AllowRecordUsage(RecRef.RECORDID);
            RecRef.SETTABLE(ApprovalEntry);
            RecRef.GET(ApprovalEntry."Record ID to Approve");
            AllowRecordUsage(RecRef);
        END;
    DATABASE::"Gen. Journal Batch":
        BEGIN
            RecRef.SETTABLE(GenJournalBatch);
            RecordRestrictionMgt.AllowGenJournalBatchUsage(GenJournalBatch);
        END
    ELSE
        RecordRestrictionMgt.AllowRecordUsage(RecRef.RECORDID);
END;
```

Notice how lifting a restriction for a Gen. Journal Batch involves lifting all the restrictions for the individual journal lines in the batch (hence the special branching of the code).

****

**Consequences**

Currently, there can only be one restriction per record. There are no restriction types.

In the future, a type field should be added to the restriction table, to allow adding restrictions for different purposes, and to refine their consumption. For example, a posting restriction might only be enforced for restrictions originating from approvals.

**NAV Versions**

This pattern has been introduced in Dynamics NAV 2016\.



[anchor0]: attention.jpg
[anchor1]: /nav/w/designpatterns/79.blocked-entity
[anchor2]: https://microsoft.sharepoint.com/teams/DynamicsNAV/Wiki/Nav%20Wiki%20Documents/NAV%20App%20Patterns/NAV%20App%20Patterns%20for%20Review/Data-Driven%20Blocked%20Entity.docx#_msocom_2


[image0]: attention.jpg
