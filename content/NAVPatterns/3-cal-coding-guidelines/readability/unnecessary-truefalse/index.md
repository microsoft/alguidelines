+++
title = "Unnecessary TRUE/FALSE"
weight = 1300
+++
Do not use TRUE or FALSE keywords unnecessarily if the expression is already an logical expression.

Bad code

```al
IF IsPositive() = TRUE THEN  
```      
    

Good code

```al
IF IsPositive THEN  
```      


Bad code

```
IF Complete <> TRUE THEN  
```      
    

Good code

```al
IF NOT Complete THEN
```
