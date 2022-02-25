---
title: "CASE Action on next line"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

A CASE action should start on a line after the possibility.

## Bad code

```AL
    case Letter of
        'A': Letter2 := '10';
        'B': Letter2 := '11';
    end;
```

## Good code

```AL
    case Letter of
        'A':
            Letter2 := '10';
        'B':
            Letter2 := '11';
    end;
```
