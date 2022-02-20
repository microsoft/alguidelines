+++
title = "Unnecessary Compound Parenthesis"
weight = 1260
+++
Use parenthesis only to enclose compound expressions inside compound expressions.

Bad code

```al
IF ("Costing Method" = "Costing Method"::Standard) THEN  
```
    

Good code

```al
IF "Costing Method" = "Costing Method"::Standard THEN  
```
 

Bad code

```al
ProfitPct = -(Profit) / CostAmt * 100;  
```
    

Good code

```al
ProfitPct = -Profit / CostAmt * 100;
```
