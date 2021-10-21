+++
title = "variable-naming.md"
weight = 1420
+++
Variables that refer to a C/AL object must contain the objects name, abbreviated where necessary.

A variable must begin with a capital letter.

Blanks, periods, and other characters (such as parentheses) that would make quotation marks around a variable necessary must be omitted.

If a variable is a compound of two or more words or abbreviations, each word or abbreviation should begin with a capital letter.
Bad code

    ...
    WIPBuffer@1002 : Record 1018
    ...
    OBJECT Table Job WIP Buffer

Good code

    ...
    JobWIPBuffer@1002 : Record 1018
    ...
    OBJECT Table Job WIP Buffer 

Bad code

    ...
    Postline@1004 : Codeunit 12;
    ...
    OBJECT Codeunit Gen. Jnl.-Post Line

Good code

    ...
    GenJnlPostLine@1004 : Codeunit 12;
    ...
    OBJECT Codeunit Gen. Jnl.-Post Line 

Bad code

    LOCAL PROCEDURE HandleCustDebitCredit@17(...;"Amount (LCY)"@1001 : Decimal;...);
    BEGIN
    IF ((... ("Amount (LCY)" \> 0)) ...) OR
    ((... ("Amount (LCY)" < 0)) ...)
    THEN BEGIN
    ...

Good code

    LOCAL PROCEDURE HandleCustDebitCredit@17(...;AmountLCY@1001 : Decimal;...);
    BEGIN
    IF ((... (AmountLCY \> 0)) ...) OR
    ((... (AmountLCY < 0)) ...)
    THEN BEGIN
    ...
