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

## Bad code

```al
    StartingDateFilter: Text;
    Vendor: Record Vendor;
```

## Good code

```al
    Vendor: Record Vendor;
    StartingDateFilter: Text;
```

## Tips

The [AZ AL Dev Tools/AL Code Outline](https://marketplace.visualstudio.com/items?itemName=andrzejzwierzchowski.al-code-outline) extension adds two new commands to Visual Studio Code to sorts variables.

- `Sort Variables in the Active Editor` : sorts variables in the current editor
- `Sort Variables in the Active Project` : sorts variables in the current project
