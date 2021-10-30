+++
title = "Unused Initialized Variables"
weight = 1320
+++
The value assigned to a variable must be used. Else the variable is not necessary. 

Bad code

    PROCEDURE AddEntities@1(FilterStr@1000 : Text\[250\]);
    VAR
    Vendor@1001 : Record 23;
    Count@1002 : Integer;
    BEGIN
    Count := 0;
    Vendor.SETFILTER("No.",FilterStr);
    IF Vendor.FINDSET THEN
    REPEAT
    "User ID" := USERID;
    "Vendor No." := Vendor."No.";
    IF INSERT THEN
    Count += 1;
    UNTIL Vendor.NEXT = 0;
    END;

Good code

    PROCEDURE AddEntities@1(FilterStr@1000 : Text\[250\]);
    VAR
    Vendor@1001 : Record 23;
    BEGIN
    Vendor.SETFILTER("No.",FilterStr);
    IF Vendor.FINDSET THEN
    REPEAT
    "User ID" := USERID;
    "Vendor No." := Vendor."No.";
    IF INSERT THEN;
    UNTIL Vendor.NEXT = 0;
    END;
