+++
title = "One Statement Per Line"
weight = 910
+++
A line of code should not have more than one statement.

Bad code

```al
IF OppEntry.FIND('-') THEN EXIT

``` 
    

Good code

```al
IF OppEntry.FIND('-') THEN   
  EXIT  

```
    

Bad code

```al
TotalCost += Cost; TotalAmt += Amt;

```    

Good code

```al
TotalCost += Cost; 
TotalAmt += Amt;
```
