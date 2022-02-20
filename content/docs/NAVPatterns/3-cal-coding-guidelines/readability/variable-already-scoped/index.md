+++
title = "Variable Already Scoped"
weight = 1400
+++
Do not use scope ''.'' qualifier unnecessarily when a variable is already implicitly or explicitly scoped. It keeps the code simpler.

Bad code

```al
ReturnRcptHeader.SETRANGE(ReturnRcptHeader."Return Order No.","Document No.");  
```      
    

Good code

```al
ReturnRcptHeader.SETRANGE("Return Order No.","Document No.");  
```      
    

Bad code

```al
WITH ChangeLogSetupTable DO BEGIN
    ...
    IF ChangeLogSetupTable.DELETE THEN
        ...
END;
```

Good code

```al
WITH ChangeLogSetupTable DO BEGIN
    ...
    IF DELETE THEN
        ...
END;
```
