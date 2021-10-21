+++
title = "unnecessary-else.md"
weight = 1270
+++
ELSE should not be used when the last action in the THEN part is an EXIT, BREAK, SKIP, QUIT, ERROR. Bad code

    IF IsAdjmtBinCodeChanged THEN
    ERROR(AdjmtBinCodeChangeNotAllowedErr,...)
    ELSE
    ERROR(BinCodeChangeNotAllowedErr,...);

Good code

    IF IsAdjmtBinCodeChanged THEN
    ERROR(AdjmtBinCodeChangeNotAllowedErr,...)
    ERROR(BinCodeChangeNotAllowedErr,...);
