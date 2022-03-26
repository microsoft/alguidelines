---
title: "Lonely Repeat"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

The `repeat` statement should always be alone on a line.

## <span style="color:FireBrick">Bad code</span>

```al
    if ReservEntry.FindSet() then repeat
```

## <span style="color:ForestGreen">Good code</span>

```al
    if ReservEntry.FindSet() then 
        repeat
```
