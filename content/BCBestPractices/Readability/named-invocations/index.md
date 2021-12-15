+++
title = "Named Invocations"
weight = 830
+++

<_Created by Microsoft, Described by waldo_\>

## Description
When calling an object statically use the Object Name, not the Object Id.

## Bad code

```al
    Page.RunModal(525,SalesShptLine);
```

## Good code

```al
    Page.RunModal(Page::"Posted Sales Shipment Lines", SalesShptLine);
```

## [Discussions](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices?discussions_q=named+invocations+category%3A%22BC+Best+Practices%22)

You can find discussions on all "Best Practices" [here](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices).

If you don't find the discussion of this guideline, please feel fee to create a new one with the same title as this article. 
