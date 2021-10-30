+++
title = "Unary Operator Line End"
weight = 1250
+++
Do not end a line with unary operator.
Bad code

    "Quantity Handled (Base)" := -
    "Quantity Handled (Base)");

Good code

    "Quantity Handled (Base)" :=
    - "Quantity Handled (Base)");
