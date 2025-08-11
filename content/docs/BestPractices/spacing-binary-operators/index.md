---
title: "Spacing Binary Operators"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

There must be exactly one space character on each side of a binary operator such as = + - AND OR =. The parameter comma operator however, should have a space after the comma.

## Example 1

### Bad code

```al
    "Line Discount %" := "Line Discount Amount"/"Line Value"*100;
```

### Good code

```al
    "Line Discount %" := "Line Discount Amount" / "Line Value" * 100;
```

## Example 2

### Bad code

```al
    StartDate := CalcDate('<+'+Format(Days+i)+'D\>',StartDate);
```

### Good code

```al
    StartDate := CalcDate('<+' + Format(Days + i) + 'D\>', StartDate);
```

## Example 3

### Bad code

```al
    StartDate:=0D; // Initialize
```

### Good code

```al
    StartDate := 0D; // Initialize
```
