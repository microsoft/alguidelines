+++
title = "Posting Routine - Select Behavior"
weight = 940
+++
_By waldo_

## Abstract

Send information (parameters) to a processing framework/routine so that it knows what to do, how to behave.

## Description

For a processing routine to behave correctly, it needs sometimes input of a user to know what it has to do, check or avoid doing. To do this, usually a piece of UI is getting called (STRMENU) with the question what to do. These input needs to get to the routine.

1. The user selects the option on how he wants the process to behave
2. There are extra fields on the processing table, that are used internally and act like parameters. These Fields get filled according to the selections that the user has made
3. The processing codeunit receives the processing table, and therefor also the parameters

[![ ][image0]][anchor0]

## Usage

Example: Sales Post.

On Sales Header, there were fields created which act like internal parameter-fields for the "Sales Post" routine:

[![ ][image1]][anchor1]

When pressing "Post", the Selection Codeunit is getting called:

[![ ][image2]][anchor2]

Or in case of the Post&Print, the selection codeunit is different:

[![ ][image3]][anchor3]

Next, the user is able to select the options with an STRMENU, which results in filling in the fields above, like:

Selection := STRMENU(Text000,3);  
IF Selection = 0 THEN  
EXIT;  
Ship := Selection IN \[1,3\];  
Invoice := Selection IN \[2,3\];

The processing codeunit is being called after these options were set.

## NAV Usages

This is also being used in the Purchase Post.

However, the Service Post works different. In Short:

* There were no parameter fields added to the processing table (Service Header)
* The processing routine isn't called by CODEUNIT.RUN, but a function in a declared codeunit, where you pass the Invoice and Ship parameter separately.

## Ideas for improvement

Implement it consequently. There is (in my knowledge) no reason to do Service Posting differently from Sales and Purchase.

Furthermore, one might argue if adding fields to a table (which also means adding fields to the SQL Table) is the right solution to pass parameters to processing methods.

On the other hand, as we are handling tables as being "classes" in many cases, it does make sense to add "properties" to those "classes" to change the behavior of the method (SalesHeader.Post).

## Related Topics

I would like to add a pattern like "Using Argument tables" (as a sub-pattern for the facade-pattern). It somewhat is related to this way of handling parameters: using tablefields to pass a flexible amount of parameters to functions/codeunits.

{{< youtube SxywT2XSpcI>}}



[anchor0]: 7585.PostingRoutineSelectBehaviour1.png
[anchor1]: 1563.PostingRoutineSelectBehaviour2.png
[anchor2]: 5428.PostingRoutineSelectBehaviour3.png
[anchor3]: 6170.PostingRoutineSelectBehaviour4.png
[anchor4]: https://www.youtube.com/watch?v=SxywT2XSpcI&list=PLhZ3P-LY7CqmVszuvtJLujFyHpsVN0U_w&index=7


[image0]: 7585.PostingRoutineSelectBehaviour1.png
[image1]: 1563.PostingRoutineSelectBehaviour2.png
[image2]: 5428.PostingRoutineSelectBehaviour3.png
[image3]: 6170.PostingRoutineSelectBehaviour4.png
