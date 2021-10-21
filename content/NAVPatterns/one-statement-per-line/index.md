+++
title = "one-statement-per-line.md"
weight = 910
+++
A line of code should not have more than one statement.
Bad code

    IF OppEntry.FIND('-') THEN EXIT  
      
    

Good code

    IF OppEntry.FIND('-') THEN   
    EXIT  
      
    

Bad code

    TotalCost += Cost; TotalAmt += Amt;  
      
    

Good code

    TotalCost += Cost; 
    TotalAmt += Amt;
