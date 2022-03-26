---
title: "Line Start Keywords"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---
_Created by Microsoft, Described by waldo_

## Description

The `end`, `if`, `repeat`, `for`, `while`, `else` and `case` statement should always start a line. 

## <span style="color:FireBrick">Bad code</span>

```al
    if IsContactName then ValidateContactName()
    else if IsSalespersonCode then ValidateSalespersonCode()
    else if IsSalesCycleCode then ValidatSalesCycleCode();
```

## <span style="color:ForestGreen">Good code</span>

```al
    if IsContactName then
        ValidateContactName()
    else
        if IsSalespersonCode then
            ValidateSalespersonCode()
        else
            if IsSalesCycleCode then 
                ValidatSalesCycleCode();
```
