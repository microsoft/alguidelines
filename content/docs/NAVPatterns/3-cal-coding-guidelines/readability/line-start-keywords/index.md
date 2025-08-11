+++
title = "Line Start Keywords"
weight = 740
tags = ["C/AL","Readability"]
categories = ["Best Practice"]
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
