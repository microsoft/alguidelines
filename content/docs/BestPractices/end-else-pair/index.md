---
title: "end else pair"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

The `end else` pair should always appear on the same line.

## Bad code

```al
    if OppEntry.Find('-') then
        if SalesCycleStage.Find('-') then begin
        ...
        end
        else
        begin
        ... 
        end;
```

## Good code

```al
    if OppEntry.Find('-') then
        if SalesCycleStage.Find('-') then begin
        ...
        end else begin
        ...
        end;
```
