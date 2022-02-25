+++
title = "Lonely Repeat"
weight = 760
tags = ["C/AL","Readability"]
categories = ["Best Practice"]
+++
The REPEAT statement should always be alone on a line.

Bad code

```al
IF ReservEntry.FINDSET THEN REPEAT
 
```      
    

Good code

```al
IF ReservEntry.FINDSET THEN
  REPEAT
```