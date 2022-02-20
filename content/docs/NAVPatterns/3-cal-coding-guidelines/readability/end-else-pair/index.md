+++
title = "END ELSE Pair"
weight = 540
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