---
title: "One Statement per Line"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

A line of code should not have more than one statement.

## Bad code

```al
    if OppEntry.Find('-') then exit;  
```

## Good code

```al
    if OppEntry.Find('-') then   
        exit;  
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
