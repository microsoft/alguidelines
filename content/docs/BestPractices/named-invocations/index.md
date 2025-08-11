---
title: "Named Invocations"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

When calling an object statically use the Object Name, not the Object Id.

## Bad code

```al
    Page.RunModal(525, SalesShptLine);
```

## Good code

```al
    Page.RunModal(Page::"Posted Sales Shipment Lines", SalesShptLine);
```

## Tips

The [BusinessCentral.LinterCop](https://marketplace.visualstudio.com/items?itemName=StefanMaron.businesscentral-lintercop) extension adds a new rule to check your code for hardcoded object IDs.

- [LC0012](https://github.com/StefanMaron/BusinessCentral.LinterCop/wiki/LC0012): Using hardcoded IDs in functions like Codeunit.Run() is not allowed.
