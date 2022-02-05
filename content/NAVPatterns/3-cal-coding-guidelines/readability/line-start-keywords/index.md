+++
title = "Line Start Keywords"
weight = 740
+++
The END, IF, REPEAT, FOR, WHILE, ELSE and CASE statement should always start a line.

Bad code

```al
IF IsContactName THEN ValidateContactName
  ELSE IF IsSalespersonCode THEN ValidateSalespersonCode
    ELSE IF IsSalesCycleCode THEN ValidatSalesCycleCode;
```

Good code

```al
IF IsContactName THEN
  ValidateContactName
ELSE
  IF IsSalespersonCode THEN
    ValidateSalespersonCode
  ELSE
    IF IsSalesCycleCode THEN
      ValidatSalesCycleCode;
```
