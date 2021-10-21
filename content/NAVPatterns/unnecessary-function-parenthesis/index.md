+++
title = "unnecessary-function-parenthesis.md"
weight = 1280
+++
Do not use parenthesis in a function call if the function does not have any parameters.
Bad code

    IF ReservMgt.IsPositive() THEN  
      
    

Good code

    IF ReservMgt.IsPositive THEN  
      
    

Bad code

    IF ChangeStatusForm.RUNMODAL() <\> ACTION::Yes THEN  
      
    

Good code

    IF ChangeStatusForm.RUNMODAL <\> ACTION::Yes THEN
