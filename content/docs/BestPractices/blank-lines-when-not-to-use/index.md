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

## Tips

The [AZ AL Dev Tools/AL Code Outline](https://marketplace.visualstudio.com/items?itemName=andrzejzwierzchowski.al-code-outline) extension adds two new commands to Visual Studio Code to remove empty duplicate lines.

- `Remove Empty Lines from the Active Editor` : removes empty duplicate lines from the current editor
- `Remove Empty Lines from the Active Project` : removes empty duplicate lines from the current project
