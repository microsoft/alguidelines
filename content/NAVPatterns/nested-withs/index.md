+++
title = "nested-withs.md"
weight = 850
+++
Do not nest WITHs that reference different types of objects.
Bad code

    WITH PostedWhseShptLine DO BEGIN
    ...
    WITH ItemLedgEntry DO
    InsertBufferRec(...,"Serial No.","Lot No.",...);
    ...
    END;

Good code

    WITH PostedWhseShptLine DO BEGIN
    ...
    InsertBufferRec(...,ItemLedgEntry."Serial No.",ItemLedgEntry."Lot No.",...);
    ...
    END;
