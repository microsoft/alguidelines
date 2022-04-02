---
title: "Seperate if and else"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

`if` and `else` statements should be on separate lines.

## Bad code

```al
    if Atom = '>' then HasLogicalOperator := true else begin
        ...
    end;
```

## Good code

```al
    if Atom = '>' then
        HasLogicalOperator := true
    else begin
        ...
    end;
```
