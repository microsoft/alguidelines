+++
title = "Blank Lines"
weight = 260
+++
Do not use blank lines at the beginning or end of any functions, after BEGIN, before END, or inside multiline expressions.

Bad code

```al
PROCEDURE MATRIX_OnDrillDown@1133(MATRIX_ColumnOrdinal : Integer);
BEGIN

    SetupDrillDownCol(MATRIX_ColumnOrdinal);
    DrillDown(FALSE,ValueType);

END;
```

Good code

```al
PROCEDURE MATRIX_OnDrillDown@1133(MATRIX_ColumnOrdinal : Integer);
BEGIN
    SetupDrillDownCol(MATRIX_ColumnOrdinal);
    DrillDown(FALSE,ValueType);
END;
```

Bad code

```al
IF NameIsValid AND

    Name2IsValid
THEN
```

Good code

```al
IF NameIsValid AND
    Name2IsValid
THEN
```
