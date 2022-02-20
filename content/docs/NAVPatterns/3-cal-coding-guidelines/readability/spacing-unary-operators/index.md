+++
title = "Spacing Unary Operators"
weight = 1140
+++
There must be no space between a unary operator and its argument (except for the NOT keyword).

Bad code

```al
IF NOT(Type = Type::Item) THEN  
```      
    

Good code

```al
IF NOT (Type = Type::Item) THEN  
``` 
    

Bad code

```al
DiscAmt := - "Discount Amount";  
``` 
    

Good code

```al
DiscAmt := -"Discount Amount";
```
