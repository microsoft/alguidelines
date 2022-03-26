---
title: "One Statement per Line"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

A line of code should not have more than one statement.

## <span style="color:FireBrick">Bad code</span>

```al
    if OppEntry.Find('-') then exit;  
```

## <span style="color:ForestGreen">Good code</span>

```al
    if OppEntry.Find('-') then   
        exit;  
```

## <span style="color:FireBrick">Bad code</span>

```al
    TotalCost += Cost; TotalAmt += Amt;  
```

## <span style="color:ForestGreen">Good code</span>

```al
    TotalCost += Cost; 
    TotalAmt += Amt;
```
