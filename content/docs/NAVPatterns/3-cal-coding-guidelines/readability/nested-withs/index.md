+++
title = "Nested WITHs"
weight = 850
tags = ["C/AL","Readability"]
categories = ["Best Practice"]
+++
Do not nest WITHs that reference different types of objects.

Bad code

```al
WITH PostedWhseShptLine DO BEGIN
  ...
  WITH ItemLedgEntry DO
    InsertBufferRec(...,"Serial No.","Lot No.",...);
    ...
END;
```

Good code

```al
WITH PostedWhseShptLine DO BEGIN
  ...
  InsertBufferRec(...,ItemLedgEntry."Serial No.",ItemLedgEntry."Lot No.",...);
  ...
END;
```
