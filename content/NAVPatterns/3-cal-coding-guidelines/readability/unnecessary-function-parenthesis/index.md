+++
title = "Unnecessary Function Parenthesis"
weight = 1280
+++
Do not use parenthesis in a function call if the function does not have any parameters.

Bad code

```al
IF ReservMgt.IsPositive() THEN  
```
    

Good code

```al
IF ReservMgt.IsPositive THEN  
```
    

Bad code

```al
IF ChangeStatusForm.RUNMODAL() <> ACTION::Yes THEN  
```
    

Good code

```al
IF ChangeStatusForm.RUNMODAL <> ACTION::Yes THEN
```
