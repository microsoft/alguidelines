---
title: "end else pair"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

The `end else` pair should always appear on the same line.

## <span style="color:FireBrick">Bad code</span>

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

## <span style="color:ForestGreen">Good code</span>

```al
    if OppEntry.Find('-') then
        if SalesCycleStage.Find('-') then begin
        ...
        end else begin
        ...
        end;
```
