+++
title = "Variables Declarations Order"
weight = 1430
+++

<_Created by Microsoft, Described by waldo_\>

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
    StartingDateFilter : Text;
    Vendor : Record Vendor;
```

## Good code

```al
    Vendor : Record Vendor;
    StartingDateFilter : Text;
```

## [Discussions](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices?discussions_q=one+variables+declarations+order+category%3A%22BC+Best+Practices%22)

You can find discussions on all "Best Practices" [here](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices).

If you don't find the discussion of this guideline, please feel fee to create a new one with the same title as this article.  