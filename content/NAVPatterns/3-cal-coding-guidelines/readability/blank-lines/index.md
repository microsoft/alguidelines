+++
title = "Blank Lines"
weight = 260
+++
Do not use blank lines at the beginning or end of any functions, after BEGIN, before END, or inside multiline expressions. Bad code

    PROCEDURE MATRIX\_OnDrillDown@1133(MATRIX\_ColumnOrdinal : Integer);
    BEGIN
    SetupDrillDownCol(MATRIX\_ColumnOrdinal);
    DrillDown(FALSE,ValueType);
    END;

Good code

    PROCEDURE MATRIX\_OnDrillDown@1133(MATRIX\_ColumnOrdinal : Integer);
    BEGIN
    SetupDrillDownCol(MATRIX\_ColumnOrdinal);
    DrillDown(FALSE,ValueType);
    END;

Bad code

    IF NameIsValid AND
    Name2IsValid
    THEN

Good code

    IF NameIsValid AND
    Name2IsValid
    THEN
