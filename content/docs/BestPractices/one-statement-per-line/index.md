---
title: "One Statement per Line"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

A line of code should not have more than one statement.

## <span style="color:red">Bad code</span>

```al
    if OppEntry.Find('-') then exit;  
```

## <span style="color:lime">Good code</span>

```al
    if OppEntry.Find('-') then   
        exit;  
```

## <span style="color:red">Bad code</span>

```al
    TotalCost += Cost; TotalAmt += Amt;  
```

## <span style="color:lime">Good code</span>

```al
    TotalCost += Cost; 
    TotalAmt += Amt;
```
