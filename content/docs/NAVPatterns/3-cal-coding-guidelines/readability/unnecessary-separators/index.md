+++
title = "Unnecessary Separators"
weight = 1290
tags = ["C/AL","Readability"]
categories = ["Best Practice"]
+++
There should be no unnecessary separators.

Bad code

```al
IF Customer.FINDFIRST THEN;;  
```      
    

Good code

```al
IF Customer.FINDFIRST THEN;
```
