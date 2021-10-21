+++
title = "begin-end.md"
weight = 240
+++
Only use BEGIN..END to enclose compound statements.
Bad code

    IF FINDSET THEN BEGIN
    REPEAT
    ...
    UNTIL NEXT = 0;
    END;

Good code

    IF FINDSET THEN
    REPEAT
    ...
    UNTIL NEXT = 0;

Bad code

    IF IsAssemblyOutputLine THEN BEGIN
    TESTFIELD("Order Line No.",0);
    END;

Good code

    IF IsAssemblyOutputLine THEN
    TESTFIELD("Order Line No.",0);

Bad code

    IF FINDSET THEN
    REPEAT
    BEGIN
    ...
    END;
    UNTIL NEXT = 0;

Good code

    IF FINDSET THEN
    REPEAT
    ...
    UNTIL NEXT = 0;

Exception

    // Except for this case
    IF X THEN BEGIN
    IF Y THEN 
    DO SOMETHING;
    END ELSE (not X)
