---
title: "Keyword Pairs - Indentation"
tags: ["Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description
The `if..then` pair, `while..do` pair, and `for..do` pair must appear on the same line or the same level of indentation.  If possible, you can align the lines it is even much more readable.

## Bad code

```al
    if (x = y) and
      (a = b) then
```

## Good code

```al
    if (x = y) and
       (a = b) 
    then
```

## [Discussions](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices?discussions_q=keyword+pair+indentation+category%3A%22BC+Best+Practices%22)

You can find discussions on all "Best Practices" [here](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices).

If you don't find the discussion of this guideline, please feel free to create a new one with the same title as this article. 