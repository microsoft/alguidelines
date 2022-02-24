+++
title = "Indentation"
weight = 650
tags = ["C/AL","Readability"]
categories = ["Best Practice"]
+++
In general, use an indentation of two space characters. Logical expressions in the IF, WHILE, and UNTIL parts are indented at least 3, 6, and 6 spaces respectively.

Bad code

```al
IF GLSetup."Unrealized VAT" OR
  (GLSetup."Prepayment Unrealized VAT" AND NewCVLedgEntryBuf.Prepayment)
```

Good code

```al
IF GLSetup."Unrealized VAT" OR
    (GLSetup."Prepayment Unrealized VAT" AND NewCVLedgEntryBuf.Prepayment)
```

Bad code

```al
IF GenJnlLine."Account No." <> ICPartner.Code THEN
  ICPartner.GET("Account No.");
  IF GenJnlLine.Amount \> 0 THEN BEGIN
    ...
```

Good code

```al
IF GenJnlLine."Account No." <> ICPartner.Code THEN
  ICPartner.GET("Account No.");
IF GenJnlLine.Amount > 0 THEN BEGIN
  ...
```

Bad code

```al
Dialog.OPEN(WindowTxt +
            '@1@@@@@@@@@@@@@@@@@@@@@@@');
```

Good code

```al
Dialog.OPEN(
    WindowTxt +
    '@1@@@@@@@@@@@@@@@@@@@@@@@');
```

Bad code

```al
TempOldCustLedgEntry.DELETE;
  // Find the next old entry for application of the new entry
```

Good code

```al
TempOldCustLedgEntry.DELETE;
// Find the next old entry for application of the new entry
```

Bad code

```al
IF NOT ("Applies-to Doc. Type" IN
   ["Applies-to Doc. Type"::Receipt,
    "Applies-to Doc. Type"::"Return Shipment"])
```

Good code

```al
IF NOT ("Applies-to Doc. Type" IN
        ["Applies-to Doc. Type"::Receipt,
         "Applies-to Doc. Type"::"Return Shipment"])
```

Bad code

```al
WHILE (RemAmt > 0) OR
  (RemAmtLCY > 0)
DO
```

Good code

```al
WHILE (RemAmt > 0) OR
      (RemAmtLCY > 0)
DO
```

Bad code

```al
UNTIL (RemAmt > 0) AND
  (RemAmtLCY > 0);
```

Good code

```al
UNTIL (RemAmt > 0) AND
      (RemAmtLCY > 0)
```
