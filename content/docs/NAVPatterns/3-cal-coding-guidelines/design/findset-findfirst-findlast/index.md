+++
title = "FINDSET FINDFIRST FINDLAST"
weight = 600
tags = ["C/AL"]
categories = ["Best Practice"]
+++
FINDSET, FIND('+') or FIND('-') should only be used when NEXT is used and vice versa.

Bad code

    IF Cust.FIND('-') THEN
        ERROR(CustIsBlockErr)

Good code

    IF Cust.FINDFIRST THEN
        ERROR(CustIsBlockErr)

Bad code

    IF Cust.FINDFIRST THEN
        REPEAT
            ...
        UNTIL Cust.NEXT = 0;

Good code

    IF Cust.FINDSET THEN
        REPEAT
            ...
        UNTIL Cust.NEXT = 0;
