+++
title = "variable-already-scoped.md"
weight = 1400
+++
Do not use scope ''.'' qualifier unnecessarily when a variable is already implicitly or explicitly scoped. It keeps the code simpler.
Bad code

    ReturnRcptHeader.SETRANGE(ReturnRcptHeader."Return Order No.","Document No.");  
      
    

Good code

    ReturnRcptHeader.SETRANGE("Return Order No.","Document No.");  
      
    

Bad code

    WITH ChangeLogSetupTable DO BEGIN
    ...
    IF ChangeLogSetupTable.DELETE THEN
    ...
    END;

Good code

    WITH ChangeLogSetupTable DO BEGIN
    ...
    IF DELETE THEN
    ...
    END;
