---
title: "CASE Action on next line"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

A CASE action should start on a line after the possibility.

## <span style="color:FireBrick">Bad code</span>

```AL
    case Letter of
        'A': Letter2 := '10';
        'B': Letter2 := '11';
    end;
```

## <span style="color:ForestGreen">Good code</span>

```AL
    case Letter of
        'A':
            Letter2 := '10';
        'B':
            Letter2 := '11';
    end;
```
