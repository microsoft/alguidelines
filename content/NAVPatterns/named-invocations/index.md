+++
title = "named-invocations.md"
weight = 830
+++
When calling an object statically use the name, not the number
Bad code

    PAGE.RUNMODAL(525,SalesShptLine)  
      
    

Good code

    PAGE.RUNMODAL(PAGE::"Posted Sales Shipment Lines",SalesShptLine)
