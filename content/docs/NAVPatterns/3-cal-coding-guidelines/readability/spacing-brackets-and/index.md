+++
title = "Spacing Brackets and ::"
weight = 1130
+++
There must be no spaces characters before and after [] dimension brackets symbols or :: option symbols.

Bad code

```al
A[i] [j] := Amt;  
```
    

Good code

```al
A[i][j] := Amt;  
``` 
    

Bad code

```al
"Currency Exchange Rate"."Fix Exchange Rate Amount" :: Currency:  
``` 
    

Good code

```al
"Currency Exchange Rate"."Fix Exchange Rate Amount"::Currency:  
``` 
    

Bad code

```al
IF FIND (Which) THEN  
```      
    

Good code

```al
IF FIND(Which) THEN
```