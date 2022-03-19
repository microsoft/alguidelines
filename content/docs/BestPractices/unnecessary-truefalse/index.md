---
title: "Unnecessary true/false"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description
Do not use `true` or `false` keywords unnecessarily if the expression is already an logical expression.

## <span style="color:red">Bad code</span>

```al
    if IsPositive() = true then  
```

## <span style="color:lime">Good code</span>

```al
    if IsPositive() then  
```

## <span style="color:red">Bad code</span>

```al
    if Complete <> true then  
```

## <span style="color:lime">Good code</span>

```al
    if not Complete then
```
