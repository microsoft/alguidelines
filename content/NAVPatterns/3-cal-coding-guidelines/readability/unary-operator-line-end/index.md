+++
title = "Unary Operator Line End"
weight = 1250
+++
Do not end a line with unary operator.

Bad code

```al
"Quantity Handled (Base)" := -
    "Quantity Handled (Base)");
```

Good code

```al
"Quantity Handled (Base)" :=
    - "Quantity Handled (Base)");
```
