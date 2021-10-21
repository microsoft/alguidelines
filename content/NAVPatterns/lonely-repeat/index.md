+++
title = "lonely-repeat.md"
weight = 760
+++
The REPEAT statement should always be alone on a line.
Bad code

    IF ReservEntry.FINDSET THEN REPEAT  
      
    

Good code

    IF ReservEntry.FINDSET THEN
    REPEAT
