---
title: "Seperate if and else"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description
`if` and `else` statements should be on separate lines. 

## Bad code

```al
    if Atom = '\>' then HasLogicalOperator := true else begin
    ...
    end;
```

## Good code

```al
    if Atom = '\>' then
        HasLogicalOperator := true
    else begin
        ...
    end;
```


## [Discussions](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices?discussions_q=separate+if+and+else+category%3A%22BC+Best+Practices%22)

You can find discussions on all "Best Practices" [here](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices).

If you don't find the discussion of this guideline, please feel free to create a new one with the same title as this article.  
