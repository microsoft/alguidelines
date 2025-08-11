---
title: "Unnecessary true/false"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description
Do not use `true` or `false` keywords unnecessarily if the expression is already an logical expression.

## Example 1

### Bad code

```al
    if IsPositive() = true then  
```

### Good code

```al
    if IsPositive() then  
```

## Example 2

### Bad code

```al
    if Complete <> true then  
```

### Good code

```al
    if not Complete then
```
