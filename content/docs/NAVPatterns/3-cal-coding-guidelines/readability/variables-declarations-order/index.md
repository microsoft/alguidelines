+++
title = "Variables Declarations Order"
weight = 1430
tags = ["C/AL","Readability"]
categories = ["Best Practice"]
+++
Variables declarations should be ordered by type. In general, object and complex variable types are listed first followed by simple variables. The order should be the same as the object list in the object designer for C/AL objects. Afterwards come the complex variables like RecordRef, .NET, FieldRef etc. At the end come all the simple data types in no particular order.

Bad code

```al
StartingDateFilter@1002 : Text[30];
Vend@1003 : Record 23;
```

Good code

```al
Vend@1003 : Record 23;
StartingDateFilter@1002 : Text[30];
```
