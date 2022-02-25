---
title: "Unnecessary else"
tags: ["AL","Readability"]
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
