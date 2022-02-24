---
title: "Line Start Keywords"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---
_Created by Microsoft, Described by waldo_

## Description
The `end`, `if`, `repeat`, `for`, `while`, `else` and `case` statement should always start a line. 

## Bad code

```al
    if IsContactName then ValidateContactName()
    else if IsSalespersonCode then ValidateSalespersonCode()
    else if IsSalesCycleCode then ValidatSalesCycleCode();
```

## Good code

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


## [Discussions](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices?discussions_q=line+start+keyword+category%3A%22BC+Best+Practices%22)

You can find discussions on all "Best Practices" [here](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices).

If you don't find the discussion of this guideline, please feel free to create a new one with the same title as this article.  