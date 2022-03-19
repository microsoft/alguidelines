---
title: "Keyword Pairs - Indentation"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

The `if..then` pair, `while..do` pair, and `for..do` pair must appear on the same line or the same level of indentation.  If possible, you can align the lines it is even much more readable.

## <span style="color:red">Bad code</span>

```al
    if (x = y) and
      (a = b) then
```

## <span style="color:lime">Good code</span>

```al
    if (x = y) and
       (a = b) 
    then
```
