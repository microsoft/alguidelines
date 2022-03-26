---
title: "When not to use Blank Lines"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

Do not use blank lines:

- at the beginning or end of any functions (after `begin` and before `end`)
- inside multiline expressions

## Example 1

### Bad code

```al
procedure MATRIX_OnDrillDown(MATRIX_ColumnOrdinal: Integer);
begin

    SetupDrillDownCol(MATRIX_ColumnOrdinal);
    DrillDown(false, ValueType);

end;
```

### Good code

```al
procedure MATRIX_OnDrillDown(MATRIX_ColumnOrdinal: Integer);
begin
    SetupDrillDownCol(MATRIX_ColumnOrdinal);
    DrillDown(false, ValueType);
end;
```

## Example 2

### Bad code

```al
if NameIsValid and

    Name2IsValid
then
```

### Good code

```al
if NameIsValid and
    Name2IsValid
then
```
