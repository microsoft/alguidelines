+++
title = "One Statement Per Line"
weight = 910
+++

<_Created by Microsoft, Described by waldo_\>

## Description
A line of code should not have more than one statement.

## Bad code

```al
    if OppEntry.Find('-') then exit();  
```   
    

## Good code

```al
    if OppEntry.Find('-') then   
        exit();  
```   
    
## Bad code

```al
    TotalCost += Cost; TotalAmt += Amt;  
```   
    

## Good code

```al
    TotalCost += Cost; 
    TotalAmt += Amt;
```

## [Discussions](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices?discussions_q=one+statement+per+line+category%3A%22BC+Best+Practices%22)

You can find discussions on all "Best Practices" [here](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices).

If you don't find the discussion of this guideline, please feel fee to create a new one with the same title as this article.  