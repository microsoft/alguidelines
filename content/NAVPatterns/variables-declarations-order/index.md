+++
title = "variables-declarations-order.md"
weight = 1430
+++
Variables declarations should be ordered by type. In general, object and complex variable types are listed first followed by simple variables. The order should be the same as the object list in the object designer for C/AL objects. Afterwards come the complex variables like RecordRef, .NET, FieldRef etc. At the end come all the simple data types in no particular order. Bad code

    StartingDateFilter@1002 : Text\[30\];
    Vend@1003 : Record 23;

Good code

    Vend@1003 : Record 23;
    StartingDateFilter@1002 : Text\[30\];
