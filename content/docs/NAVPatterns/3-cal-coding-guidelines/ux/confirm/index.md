+++
title = "CONFIRM"
weight = 380
tags = ["C/AL"]
categories = ["Best Practice"]
+++
Always end CONFIRM with a question mark.

Bad code

```al
ChangeAllOpenedEntriesQst@1000 : TextConst 'ENU=Do you want to change all open entries for every customer and vendor that are not blocked';
...
IF CONFIRM(ChangeAllOpenedEntriesQst,TRUE) THEN
```

Good code

```al
ChangeAllOpenedEntriesQst@1000 : TextConst 'ENU=Do you want to change all open entries for every customer and vendor that are not blocked?';
...
IF CONFIRM(ChangeAllOpenedEntriesQst,TRUE) THEN
```
