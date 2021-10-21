+++
title = "confirm.md"
weight = 380
+++
Always end CONFIRM with a question mark.
Bad code

    ChangeAllOpenedEntriesQst@1000 : TextConst 'ENU=Do you want to change all open entries for every customer and vendor that are not blocked';
    ...
    IF CONFIRM(ChangeAllOpenedEntriesQst,TRUE) THEN

Good code

    ChangeAllOpenedEntriesQst@1000 : TextConst 'ENU=Do you want to change all open entries for every customer and vendor that are not blocked?';
    ...
    IF CONFIRM(ChangeAllOpenedEntriesQst,TRUE) THEN
