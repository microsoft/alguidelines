---
title: "Spacing Binary Operators"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

There must be exactly one space character on each side of a binary operator such as = + - AND OR =. The parameter comma operator however, should have a space after the comma.

## <span style="color:red">Bad code</span>

```al
    "Line Discount %" := "Line Discount Amount"/"Line Value"*100;  
```

## <span style="color:lime">Good code</span>

```al
    "Line Discount %" := "Line Discount Amount" / "Line Value" * 100;  
```

## <span style="color:red">Bad code</span>

```al
    StartDate := CalcDate('<+'+Format(Days+i)+'D\>',StartDate);  
```

## <span style="color:lime">Good code</span>

```al
    StartDate := CalcDate('<+' + Format(Days + i) + 'D\>', StartDate);  
```

## <span style="color:red">Bad code</span>

```al
    StartDate:=0D; // Initialize  
```

## <span style="color:lime">Good code</span>

```al
    StartDate := 0D; // Initialize
```
