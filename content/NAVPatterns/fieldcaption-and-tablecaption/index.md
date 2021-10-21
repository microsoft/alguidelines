+++
title = "fieldcaption-and-tablecaption.md"
weight = 580
+++
For user messages, errors etc., use FIELDCAPTION not FIELDNAME and TABLECAPTION not TABLENAME.

Reason:

1. The correct translation will be automatically used.
2. If the caption/name changes, then there will be a single point of change needed.

Bad code

    IF NOT CONFIRM(UpdateLocationQst,TRUE,FIELDNAME("Location Code"),...)

Good code

    IF NOT CONFIRM(UpdateLocationQst,TRUE,FIELDCAPTION("Location Code"),...)
