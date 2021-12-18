+++
title = "Lonely Repeat"
weight = 760
+++

<_Created by Microsoft, Described by waldo_\>

## Description
The `repeat` statement should always be alone on a line.

## Bad code

```al
    if ReservEntry.FindSet() then repeat
```
    
## Good code

```al
    if ReservEntry.FindSet() then 
        repeat
```

## [Discussions](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices?discussions_q=lonely+repeat+category%3A%22BC+Best+Practices%22)

You can find discussions on all "Best Practices" [here](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices).

If you don't find the discussion of this guideline, please feel fee to create a new one with the same title as this article.  