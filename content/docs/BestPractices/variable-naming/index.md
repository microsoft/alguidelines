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

## Example 1

### Bad code

```al
    WIPBuffer: Record "Job WIP Buffer"
```

### Good code

```al
    JobWIPBuffer: Record "Job WIP Buffer"    
```

## Example 2

### Bad code

```al
    Postline: Codeunit "Gen. Jnl.-Post Line";    
```

### Good code

```al
    GenJnlPostLine: Codeunit "Gen. Jnl.-Post Line";
```

## Example 3

### Bad code

```al
    "Amount (LCY)": Decimal;    
```

### Good code

```al
    AmountLCY: Decimal;        
```

## Tips

The [AL Variable Helper](https://marketplace.visualstudio.com/items?itemName=rasmus.al-var-helper) extension provides Intellisense support to assign correct variable names in AL.
