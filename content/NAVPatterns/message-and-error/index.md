+++
title = "MESSAGE and ERROR"
weight = 790
+++
Always end MESSAGE or ERROR with a period.
Bad code

    CustIsBlockedErr@1025 : TextConst 'ENU=You cannot %1 this type of document when Customer %2 is blocked with type %3';
    ...
    ERROR(CustIsBlockedErr,...);

Good code

    CustIsBlockedErr@1025 : TextConst 'ENU=You cannot %1 this type of document when Customer %2 is blocked with type %3.';
    ...
    ERROR(CustIsBlockedErr,...);
