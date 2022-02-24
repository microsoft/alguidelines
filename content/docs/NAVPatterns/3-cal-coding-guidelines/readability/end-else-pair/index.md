+++
title = "END ELSE Pair"
weight = 540
tags = ["C/AL","Readability"]
categories = ["Best Practice"]
+++
The END ELSE pair should always appear on the same line.

Bad code

```al
IF OppEntry.FIND('-') THEN
    IF SalesCycleStage.FIND('-') THEN BEGIN
        ...
    END
ELSE
    ...
```

Good code

```al
IF OppEntry.FIND('-') THEN
    IF SalesCycleStage.FIND('-') THEN BEGIN
        ...
END ELSE
    ...
```