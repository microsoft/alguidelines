+++
title = "unnecessary-compound-parenthesis.md"
weight = 1260
+++
Use parenthesis only to enclose compound expressions inside compound expressions.
Bad code

    IF ("Costing Method" = "Costing Method"::Standard) THEN  
      
    

Good code

    IF "Costing Method" = "Costing Method"::Standard THEN  
      
    

#### 

Bad code

    ProfitPct = -(Profit) / CostAmt \* 100;  
      
    

Good code

    ProfitPct = -Profit / CostAmt \* 100;
