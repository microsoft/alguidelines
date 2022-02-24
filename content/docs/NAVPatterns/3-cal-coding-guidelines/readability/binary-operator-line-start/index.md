+++
title = "Binary Operator to Start Line"
weight = 250
tags = ["C/AL","Readability"]
categories = ["Best Practice"]
+++
Do not start a line with a binary operator.

Bad code

```al
"Quantity to Ship" :=
    Quantity 
    - "Quantity Shipped"
```

Good code

```al
"Quantity to Ship" :=
    Quantity -
    "Quantity Shipped"
```
