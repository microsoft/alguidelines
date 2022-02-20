+++
title = "Binary Operator to Start Line"
weight = 250
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
