+++
title = "Unary Operator Line End"
weight = 1250
tags = ["C/AL","Readability"]
categories = ["Best Practice"]
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
