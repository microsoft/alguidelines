---
title: "Variables Declarations Order"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

Variables declarations should be ordered by type. In general, object and complex variable types are listed first followed by simple variables. The order should be:

- Record
- Report
- Codeunit
- XmlPort
- Page
- Query
- Notification
- BigText
- DateFormula
- RecordId
- RecordRef
- FieldRef
- FilterPageBuilder

(Ref: [Microsoft Docs](https://docs.microsoft.com/en-us/dynamics365/business-central/dev-itpro/developer/analyzers/codecop-aa0021))

## <span style="color:red">Bad code</span>

```al
    StartingDateFilter: Text;
    Vendor: Record Vendor;
```

## <span style="color:lime">Good code</span>

```al
    Vendor: Record Vendor;
    StartingDateFilter: Text;
```
