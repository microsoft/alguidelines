+++
title = "Lonely Repeat"
weight = 760
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