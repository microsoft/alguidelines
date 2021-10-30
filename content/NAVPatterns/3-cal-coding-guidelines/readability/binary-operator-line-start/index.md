+++
title = "Binary Operator to Start Line"
weight = 250
+++
Do not start a line with a binary operator. Bad code

    "Quantity to Ship" :=
    Quantity 
    - "Quantity Shipped"

Good code

    "Quantity to Ship" :=
    Quantity -
    "Quantity Shipped"
