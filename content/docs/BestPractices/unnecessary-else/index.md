---
title: "Unnecessary else"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

`else` should not be used when the last action in the `then` part is an `exit`, `break`, `skip`, `quit`, `error`. 

## <span style="color:red">Bad code</span>

```al
    procedure SomeProcedure()
    begin
        if IsAdjmtBinCodeChanged() then
            Error(AdjmtBinCodeChangeNotAllowedErr, ...)
        else
            Error(BinCodeChangeNotAllowedErr, ...);
    end;
```

## <span style="color:lime">Good code</span>

```al
    procedure SomeProcedure()
    begin
        if IsAdjmtBinCodeChanged() then
            Error(AdjmtBinCodeChangeNotAllowedErr, ...)
        Error(BinCodeChangeNotAllowedErr, ...);
    end;
```
