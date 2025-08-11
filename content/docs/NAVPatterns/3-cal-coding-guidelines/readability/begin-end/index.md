+++
title = "Begin-End - Compound Only"
weight = 240
tags = ["C/AL","Readability"]
categories = ["Best Practice"]
+++
Only use BEGIN..END to enclose compound statements.

Bad code

```al
IF FINDSET THEN BEGIN
    REPEAT
        ...
    UNTIL NEXT = 0;
END;
```

Good code

```al
IF FINDSET THEN
    REPEAT
        ...
    UNTIL NEXT = 0;
```

Bad code

```al
IF IsAssemblyOutputLine THEN BEGIN
    TESTFIELD("Order Line No.",0);
END;
```

Good code

```al
IF IsAssemblyOutputLine THEN
    TESTFIELD("Order Line No.",0);
```

Bad code

```al
IF FINDSET THEN
    REPEAT
        BEGIN
            ...
        END;
    UNTIL NEXT = 0;
```

Good code

```al
IF FINDSET THEN
    REPEAT
        ...
    UNTIL NEXT = 0;
```

Exception

```al
// Except for this case
IF X THEN BEGIN
    IF Y THEN 
        DO SOMETHING;
END ELSE (not X)
```
