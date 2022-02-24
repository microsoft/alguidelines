+++
title = "Unnecessary Compound Parenthesis"
weight = 1260
tags = ["C/AL","Readability"]
categories = ["Best Practice"]
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
