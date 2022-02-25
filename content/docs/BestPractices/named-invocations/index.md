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
