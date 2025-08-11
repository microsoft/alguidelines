+++
title = "FIELDERROR"
weight = 590
tags = ["C/AL"]
categories = ["Best Practice"]
+++
Never use FIELDERROR with a period as it is automatically inserted.

Bad code

```al
InvalidValue@1025 : TextConst 'ENU=is invalid.';
...
Cust.FIELDERROR("No.",InvalidValue);
```

Good code

```al
InvalidValue@1025 : TextConst 'ENU=is invalid';
...
Cust.FIELDERROR("No.",InvalidValue);
```
