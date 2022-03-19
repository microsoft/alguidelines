---
title: "Variable Naming"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

Variables that refer to a AL object must contain the objects name, abbreviated where necessary.

A variable must begin with a capital letter.

Blanks, periods, and other characters (such as parentheses) that would make quotation marks around a variable necessary must be omitted.

If a variable is a compound of two or more words or abbreviations, each word or abbreviation should begin with a capital letter.

## <span style="color:red">Bad code</span>

```al
    WIPBuffer: Record "Job WIP Buffer"
```

## <span style="color:lime">Good code</span>

```al
    JobWIPBuffer: Record "Job WIP Buffer"    
```

## <span style="color:red">Bad code</span>

```al
    Postline: Codeunit "Gen. Jnl.-Post Line";    
```

## <span style="color:lime">Good code</span>

```al
    GenJnlPostLine: Codeunit "Gen. Jnl.-Post Line";
```

## <span style="color:red">Bad code</span>

```al
    "Amount (LCY)": Decimal;    
```

## <span style="color:lime">Good code</span>

```al
    AmountLCY: Decimal;        
```
