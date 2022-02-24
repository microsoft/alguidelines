+++
title = "Unnecessary ELSE"
weight = 1270
tags = ["C/AL","Readability"]
categories = ["Best Practice"]
+++
ELSE should not be used when the last action in the THEN part is an EXIT, BREAK, SKIP, QUIT, ERROR.

Bad code

```al
IF IsAdjmtBinCodeChanged THEN
    ERROR(AdjmtBinCodeChangeNotAllowedErr,...)
ELSE
    ERROR(BinCodeChangeNotAllowedErr,...);
```

Good code

```al
IF IsAdjmtBinCodeChanged THEN
    ERROR(AdjmtBinCodeChangeNotAllowedErr,...)
ERROR(BinCodeChangeNotAllowedErr,...);
```
