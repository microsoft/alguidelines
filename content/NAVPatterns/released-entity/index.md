+++
title = "Released Entity"
weight = 1000
+++
Authors: Henrik Langbak and Kim Ginnerup, Bording Data

## **Abstract**

This pattern prevent data from being used elsewhere before it is in a system consistent state.  
NAV inserts a record as soon as the primary key has been set. But the record may not be in a valid state at this point in time. How do you know if a newly inserted record is ready for use?

## **Description**

Whenever you need to stall the release of data, you can use this pattern.

Because NAV decides when a record is written to the database, it may not be in a system consistent state. Nobody should use the record before everything is in place and the record is valid. An inserted record may even have data in other tables that needs to be inserted and in a valid state before other parts of the system can use the data without running into a problem.

Data entered into the system may have to be approved by a second person before it can be used.

Data requires different parties (e.g. Departments) to add information before data is valid.

The solution is an Option Field with two or three values:  
(Open, Released) or (Open, Pending, Released)

The states should be interpreted as:

State

Description

Open

Not all data is in place. The record is system inconsistent. The record or record hierarchy is invisible for all other parts of the system.

Pending

The record is system consistent. But is awaiting someone to manually raise the state to Released.  
The record is still invisible.

Released

All data is in place and the record is system consistent, and ready for use. It is now visible for the rest of the system. The state can never be reversed.

The option field name: Release State.

This pattern is very similar to the Blocked Entity pattern, but it has one significant difference.  
The record is not visible to any part of the system, before it is in the Released state.  
There is no going back. When the Released state is reached, it will stay that way for the life of the record. In case of a tri-state, it is ok to bypass Pending seen from a system state perspective.

If there is a hierarchy, e.g. Header and Lines, then the Release State field resides on the Header. As long as the Header remains unreleased, the lines are considered inconsistent and must not be used.

The important and critical part of this pattern is that the whole application needs to obey the "Release State"-contract or the system will fail. 

## **Usage**

## To use this pattern you need to create an Option Field named: "Release State" with at least the two states: Open, Released. 

### Automatic pending or release

If it is feasible to set the Release State automatically, create a local function named: "CheckAndSetReleaseState" that validates the record and sets Release State to Pending or Released, when all system requirements are met.

The function only works one way. It can only increase the state. If the function is called when release state is Pending or Release, the function will do nothing.

If the function is implemented, then the user should not be able to change the value from Open to Pending or Released, only the function can do that.

The function should be called on every change, this way it will be visible to the end user immediately when all requirements are met.   
If the Pattern is implemented without this function, then field is maintained by the end user.

## **NAV Specific Example**

NAV has Table 36 Sales Header, Field 120 Status.  
In this example, it is called: Status.  
Status has 4 values: Open, Released, Pending Approval, Pending Prepayment.

## **NAV Usages**

The example above, taken from NAV, shows similarity with this pattern, but in the Sales Header example it is possible to modify the Release state to an earlier state, through a function in NAV. This is not recommended in the pattern.

## **Ideas for improvement**

In case of a Pending State, you may need an Approved Date and Approved By. Depending on how formal it needs to be. 

## **Consequences**

This pattern is only used on data creation.   
If there is a need for shuffling between states back and forth, then this is not the pattern to use. 

## **Related Topics**

Blocked Entity is a variant of this pattern but Blocked Entity is used later in the life-cycle.

An alternative to the pattern is temporary tables. But the temporary table is an all or nothing approach. If all data in the Record is valid, the temporary record can update the real data. If not, abandon the process. The Release State pattern is simpler and requires less code. 

## **References**

There is a term called: Long Term Lock. This pattern has some resemblance.  
When talking about hierarchical data structures and the Release State is held on the root node, there is a pattern called Hierarchical Locking that has some resemblance.
