+++
title = "Initialized Variables"
weight = 660
tags = ["C/AL"]
categories = ["Best Practice"]
+++
Variables should always be set to a specific value, before they are used.

Bad code

    PROCEDURE SetPurchLine@22(VAR CurrentPurchLine@1000 : Record 39);
    VAR
        Pegging@1001 : Boolean;
    BEGIN
        IF Pegging THEN
            CurrQuantity := CurrentPurchLine."Quantity (Base)"
        ELSE
            CurrQuantity := CurrentPurchLine."Outstanding Qty. (Base)";
    END;

Good code

    PROCEDURE SetPurchLine@22(VAR CurrentPurchLine@1000 : Record 39);
    VAR
        Pegging@1001 : Boolean;
    BEGIN
        Pegging := IsPegging(CurrentPurchLine);
        IF Pegging THEN
            CurrQuantity := CurrentPurchLine."Quantity (Base)"
        ELSE
            CurrQuantity := CurrentPurchLine."Outstanding Qty. (Base)";
    END;

Bad code

    // In the example below, the function will always return FALSE.
    PROCEDURE GetItemsToPlan@22() : Boolean;
    BEGIN
        SETRANGE("Document Type","Document Type"::Order);
            ...
        FINDSET
    END;

Good code

    PROCEDURE GetItemsToPlan@22() : Boolean;
    BEGIN
        SETRANGE("Document Type","Document Type"::Order);
            ...
        EXIT(FINDSET)
    END;
