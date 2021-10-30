+++
title = "Spacing Binary Operators"
weight = 1120
+++
There must be exactly one space character on each side of a binary operator such as = + - AND OR =. The parameter comma operator however, should have no spaces.
Bad code

    "Line Discount %" := "Line Discount Amount"/"Line Value"\*100  
      
    

Good code

    "Line Discount %" := "Line Discount Amount" / "Line Value" \* 100;  
      
    

Bad code

    StartDate := CALCDATE('<+'+FORMAT(Days + i)+'D\>', StartDate);  
      
    

Good code

    StartDate := CALCDATE('<+' + FORMAT(Days + i) + 'D\>',StartDate);  
      
    

Bad code

    StartDate := 0D; // Initialize  
      
    

Good code

    StartDate := 0D; // Initialize
