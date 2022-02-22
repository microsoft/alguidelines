---
title: "Unnecessary else"
tags: ["Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

`else` should not be used when the last action in the `then` part is an `exit`, `break`, `skip`, `quit`, `error`. 

## Bad code

```al
    procedure SomeProcedure()
    begin
        if IsAdjmtBinCodeChanged() then
            Error(AdjmtBinCodeChangeNotAllowedErr, ...)
        else
            Error(BinCodeChangeNotAllowedErr, ...);
    end;
```

## Good code

```al
    procedure SomeProcedure()
    begin
        if IsAdjmtBinCodeChanged() then
            Error(AdjmtBinCodeChangeNotAllowedErr, ...)
        Error(BinCodeChangeNotAllowedErr, ...);
    end;
```

## [Discussions](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices?discussions_q=unnecessary+else+category%3A%22BC+Best+Practices%22)

You can find discussions on all "Best Practices" [here](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices).

If you don't find the discussion of this guideline, please feel free to create a new one with the same title as this article.
