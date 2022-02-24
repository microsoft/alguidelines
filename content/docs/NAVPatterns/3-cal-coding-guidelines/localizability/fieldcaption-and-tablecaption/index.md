+++
title = "FIELDCAPTION and TABLECAPTION"
weight = 580
tags = ["C/AL"]
categories = ["Best Practice"]
+++
For user messages, errors etc., use FIELDCAPTION not FIELDNAME and TABLECAPTION not TABLENAME.

Reason:

1. The correct translation will be automatically used.
2. If the caption/name changes, then there will be a single point of change needed.

Bad code

```al
IF NOT CONFIRM(UpdateLocationQst,TRUE,FIELDNAME("Location Code"),...)
```

Good code

```al
IF NOT CONFIRM(UpdateLocationQst,TRUE,FIELDCAPTION("Location Code"),...)
```
