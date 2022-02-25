---
title: "Lonely Repeat"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

The `repeat` statement should always be alone on a line.

## Bad code

```al
    if ReservEntry.FindSet() then repeat
```

## Good code

```al
    if ReservEntry.FindSet() then 
        repeat
```
