---
title: "Binary Operator to Start Line"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

Do not start a line with a binary operator. 

## <span style="color:red">Bad code</span>

```AL
"Quantity to Ship" :=
Quantity 
- "Quantity Shipped"
```

## <span style="color:lime">Good code</span>

```AL
"Quantity to Ship" :=
Quantity -
"Quantity Shipped"
```
