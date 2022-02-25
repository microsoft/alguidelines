+++
title = "Named Invocations"
weight = 830
tags = ["C/AL","Readability"]
categories = ["Best Practice"]
+++
When calling an object statically use the name, not the number

Bad code

```al
PAGE.RUNMODAL(525,SalesShptLine)  
```      
    

Good code

```al
PAGE.RUNMODAL(PAGE::"Posted Sales Shipment Lines",SalesShptLine)
```
