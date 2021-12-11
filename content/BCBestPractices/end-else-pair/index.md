+++
title = "end else pair"
weight = 540
+++

<_Created by Microsoft, Described by waldo_\>

## Description

The `end else` pair should always appear on the same line.

## Bad code

```al
    if OppEntry.Find('-') then
        if SalesCycleStage.Find('-') then begin
        ...
        end
        else
        ... 
```

## Good code

```al
    if OppEntry.Find('-') then
        if SalesCycleStage.Find('-') then begin
        ...
        end else
        ...
```

## [Discussions](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices?discussions_q=end+else/pair+category%3A%22BC+Best+Practices%22)

You can find discussions on all "Best Practices" [here](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices).

If you don't find the discussion of this guideline, please feel fee to create a new one with the same title as this article.  