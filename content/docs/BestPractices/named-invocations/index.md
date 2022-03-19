---
title: "Named Invocations"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

When calling an object statically use the Object Name, not the Object Id.

## <span style="color:red">Bad code</span>

```al
    Page.RunModal(525, SalesShptLine);
```

## <span style="color:lime">Good code</span>

```al
    Page.RunModal(Page::"Posted Sales Shipment Lines", SalesShptLine);
```
