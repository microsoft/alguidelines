+++
title = "spacing-unary-operators.md"
weight = 1140
+++
There must be no space between a unary operator and its argument (except for the NOT keyword).
Bad code

    IF NOT(Type = Type::Item) THEN  
      
    

Good code

    IF NOT (Type = Type::Item) THEN  
      
    

Bad code

    DiscAmt := - "Discount Amount";  
      
    

Good code

    DiscAmt := -"Discount Amount";
