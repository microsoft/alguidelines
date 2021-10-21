+++
title = "unnecessary-separators.md"
weight = 1290
+++
There should be no unnecessary separators. Bad code

    IF Customer.FINDFIRST THEN;;  
      
    

Good code

    IF Customer.FINDFIRST THEN;
