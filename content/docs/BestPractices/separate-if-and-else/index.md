---
title: "Seperate if and else"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

`if` and `else` statements should be on separate lines. 

## <span style="color:red">Bad code</span>

```al
    if Atom = '\>' then HasLogicalOperator := true else begin
    ...
    end;
```

## <span style="color:lime">Good code</span>

```al
    if Atom = '\>' then
        HasLogicalOperator := true
    else begin
        ...
    end;
```
