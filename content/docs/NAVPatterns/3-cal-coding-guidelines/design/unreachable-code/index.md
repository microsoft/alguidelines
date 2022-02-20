+++
title = "Unreachable Code"
weight = 1310
+++
Do not write code that will never be hit.

It affects code readability and can lead to wrong assumptions.

Bad code

    IF Type <> Type::FIELD THEN BEGIN
        ...
        ERROR(...);
        RecRef.CLOSE;
    END;  
      
    

Good code

    IF Type <> Type::FIELD THEN BEGIN
        ...
        RecRef.CLOSE;
        ERROR(...);
    END;
