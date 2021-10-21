+++
title = "unused-variables.md"
weight = 1330
+++
Do not declare variables that are unused.

Unused variables affect readability.

Bad code

    PROCEDURE CheckPostingDate@23(CaptionEntryNo@1005 : Text\[50\]);
    BEGIN
    IF GenJnlCheckLine.DateNotAllowed(PostingDate) THEN
    ERROR(DateNotAllowedErr,Caption,EntryNo)
    IF PostingDate \> MaxPostingDate THEN
    MaxPostingDate := PostingDate;
    END

Good code

    PROCEDURE CheckPostingDate@23();
    BEGIN
    IF GenJnlCheckLine.DateNotAllowed(PostingDate) THEN
    ERROR(DateNotAllowedErr,Caption,EntryNo);
    IF PostingDate \> MaxPostingDate THEN
    MaxPostingDate := PostingDate;
    END;

Bad code

    PROCEDURE IsReturned@14(EntryNo@1002 : Integer) : Decimal;
    VAR
    ItemEntry@1000 : Record 32;
    Quantity@1003 : Integer;
    BEGIN
    EXIT(-OutboundApplied(EntryNo,TRUE) - InboundApplied(EntryNo,TRUE));
    END;

Good code

    PROCEDURE IsReturned@14(EntryNo@1002 : Integer) : Decimal;
    BEGIN
    EXIT(-OutboundApplied(EntryNo,TRUE) - InboundApplied(EntryNo,TRUE));
    END;
