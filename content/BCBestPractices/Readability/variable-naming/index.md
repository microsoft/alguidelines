+++
title = "Variable Naming"
weight = 1420
+++

<_Created by Microsoft, Described by waldo_\>

## Description
Variables that refer to a AL object must contain the objects name, abbreviated where necessary.

A variable must begin with a capital letter.

Blanks, periods, and other characters (such as parentheses) that would make quotation marks around a variable necessary must be omitted.

If a variable is a compound of two or more words or abbreviations, each word or abbreviation should begin with a capital letter.

## Bad code

```al
    WIPBuffer: Record "Job WIP Buffer"
```
## Good code
```al
    JobWIPBuffer: Record "Job WIP Buffer"    
```
## Bad code
```al
    Postline: Codeunit "Gen. Jnl.-Post Line";    
```
## Good code
```al
    GenJnlPostLine: Codeunit "Gen. Jnl.-Post Line";
```
## Bad code
```al
    "Amount (LCY)": Decimal;    
```
## Good code
```al
    AmountLCY: Decimal;        
```

## [Discussions](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices?discussions_q=one+variable+naming+category%3A%22BC+Best+Practices%22)

You can find discussions on all "Best Practices" [here](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices).

If you don't find the discussion of this guideline, please feel fee to create a new one with the same title as this article.  
