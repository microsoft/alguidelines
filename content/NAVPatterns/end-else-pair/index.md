+++
title = "end-else-pair.md"
weight = 540
+++
The END ELSE pair should always appear on the same line.
Bad code

    IF OppEntry.FIND('-') THEN
    IF SalesCycleStage.FIND('-') THEN BEGIN
    ...
    END
    ELSE
    ...

Good code

    IF OppEntry.FIND('-') THEN
    IF SalesCycleStage.FIND('-') THEN BEGIN
    ...
    END ELSE
    ...
