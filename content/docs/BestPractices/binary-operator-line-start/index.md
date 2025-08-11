---
title: "Binary Operator to Start Line"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

Do not start a line with a binary operator.

## Bad code

```AL
"Quantity to Ship" :=
    Quantity 
    - "Quantity Shipped"
```

## Good code

```AL
"Quantity to Ship" :=
    Quantity -
    "Quantity Shipped"
```
