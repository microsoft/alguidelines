+++
title = "Indentation"
weight = 650
+++
In general, use an indentation of two space characters. Logical expressions in the IF, WHILE, and UNTIL parts are indented at least 3, 6, and 6 spaces respectively.
Bad code

    IF GLSetup."Unrealized VAT" OR
    (GLSetup."Prepayment Unrealized VAT" AND NewCVLedgEntryBuf.Prepayment)

Good code

    IF GLSetup."Unrealized VAT" OR
    (GLSetup."Prepayment Unrealized VAT" AND NewCVLedgEntryBuf.Prepayment)

Bad code

    IF GenJnlLine."Account No." <\> ICPartner.Code THEN
    ICPartner.GET("Account No.");
    IF GenJnlLine.Amount \> 0 THEN BEGIN
    ...

Good code

    IF GenJnlLine."Account No." <\> ICPartner.Code THEN
    ICPartner.GET("Account No.");
    IF GenJnlLine.Amount \> 0 THEN BEGIN
    ...

Bad code

    Dialog.OPEN(WindowTxt +
    '@1@@@@@@@@@@@@@@@@@@@@@@@');

Good code

    Dialog.OPEN(
    WindowTxt +
    '@1@@@@@@@@@@@@@@@@@@@@@@@');

Bad code

    TempOldCustLedgEntry.DELETE;
    // Find the next old entry for application of the new entry

Good code

    TempOldCustLedgEntry.DELETE;
    // Find the next old entry for application of the new entry
    

Bad code

    IF NOT ("Applies-to Doc. Type" IN
    \["Applies-to Doc. Type"::Receipt,
    "Applies-to Doc. Type"::"Return Shipment"\])

Good code

    IF NOT ("Applies-to Doc. Type" IN
    \["Applies-to Doc. Type"::Receipt,
    "Applies-to Doc. Type"::"Return Shipment"\])

Bad code

    WHILE (RemAmt \> 0) OR
    (RemAmtLCY \> 0)
    DO

Good code

    WHILE (RemAmt \> 0) OR
    (RemAmtLCY \> 0)
    DO

Bad code

    UNTIL (RemAmt \> 0) AND
    (RemAmtLCY \> 0);

Good code

    UNTIL (RemAmt \> 0) AND
    (RemAmtLCY \> 0)
