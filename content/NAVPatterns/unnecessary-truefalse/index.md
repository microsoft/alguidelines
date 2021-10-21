+++
title = "unnecessary-truefalse.md"
weight = 1300
+++
Do not use TRUE or FALSE keywords unnecessarily if the expression is already an logical expression.
Bad code

    IF IsPositive() = TRUE THEN  
      
    

Good code

    IF IsPositive THEN  
      
    

Bad code

    IF Complete <\> TRUE THEN  
      
    

Good code

    IF NOT Complete THEN
