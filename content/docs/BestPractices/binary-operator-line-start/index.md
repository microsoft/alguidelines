---
title: "Binary Operator to Start Line"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

Do not start a line with a binary operator. 

## <span style="color:FireBrick">Bad code</span>

```AL
"Quantity to Ship" :=
Quantity 
- "Quantity Shipped"
```

## <span style="color:ForestGreen">Good code</span>

```AL
"Quantity to Ship" :=
Quantity -
"Quantity Shipped"
```
