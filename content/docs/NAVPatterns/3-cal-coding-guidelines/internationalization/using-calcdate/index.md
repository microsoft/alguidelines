+++
title = "Using Calcdate"
weight = 1370
tags = ["C/AL"]
categories = ["Best Practice"]
+++
CALCDATE should only be used with DateFormula variables. Alternatively the string should be enclosed using the <> symbols.

Bad code

    IF ReservEntry."Expected Receipt Date" >
        CALCDATE('-' + FORMAT("Dampener (Time)") + FirstDate)
    THEN

Good code

    IF ReservEntry."Expected Receipt Date" >  
        CALCDATE('<-' + FORMAT("Dampener (Time)") + FirstDate + '>')  
    THEN
