+++
title = "parameter-placeholders.md"
weight = 920
+++
The number of parameters passed to a string must match the placeholders.

****

Bad code

    CannotDeleteLineErr@1005 : TextConst 'ENU=You cannot delete this line because one or more rating values exists.';
    ...
    ERROR(CannotDeleteLineErr,TABLECAPTION);

Good code

    CannotDeleteLineErr@1005 : TextConst 'ENU=You cannot delete this line because one or more rating values exists.';
    ...
    ERROR(CannotDeleteLineErr);

### 

Bad code

    CannotUseThisFieldErr@1020 : TextConst 'ENU=You cannot use this field for %2 fields.';
    ...
    ERROR(CannotUseThisFieldErr,0,Field.Class);

Good code

    CannotUseThisFieldErr@1020 : TextConst 'ENU=You cannot use this field for %1 fields.';
    ...
    ERROR(CannotUseThisFieldErr,Field.Class);

###
