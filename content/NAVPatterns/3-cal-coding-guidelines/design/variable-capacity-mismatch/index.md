+++
title = "Variable Capacity Mismatch"
weight = 1410
+++
Do not assign a value to a variable whose capacity is smaller.

It will throw an error at runtime.

Bad code

    FileName@1010 : Text[250];
    ...
    UploadedFileName@1016 : Text[1024];
    ...
    FileName := UploadedFileName;

Good code

    FileName@1010 : Text[1024];
    ...
    UploadedFileName@1016 : Text[1024];
    ...
    FileName := UploadedFileName;

Bad code

    FileName@1010 : Text[250];
    ...
    UploadedFileName@1016 : Text[1024];
    ...
    FileName := UploadedFileName;

Good code

    FileName@1010 : Text[250];
    ...
    UploadedFileName@1016 : Text[1024];
    ...
    FileName := COPYSTR(UploadedFileName,1,250); // In case only the first 250 chars are needed. Similar for fields

Bad code

    VAR
        ExceededNumberTxt@001 : 'ENU=Warning: Exceeded number of unsent documents/requests'
        Subject@1002 : Text[50];
        ...
    BEGIN
        ...
        Subject := ExceededNumberTxt;

Good code

    VAR
        ExceededNumberTxt@001 : 'ENU=Warning: Exceeded number of unsent documents/requests'
        Subject@1002 : Text[100];
        ...
    BEGIN
        ...
        Subject := ExceededNumberTxt';
