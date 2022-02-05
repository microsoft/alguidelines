+++
title = "Global Text Constants"
weight = 610
+++
Declare Text Constant as global variables.

Bad code

```al
PROCEDURE GetRequirementText@6(...) : Text[50];
VAR
    RequirementOptionsTxt@1002 : TextConst 'ENU=Shipment,Receive,Pick,Put-Away';
BEGIN
```

Good code

```al
VAR
    RequirementOptionsTxt@1002 : TextConst 'ENU=Shipment,Receive,Pick,Put-Away';
...
PROCEDURE GetRequirementText@6(...) : Text[50];
BEGIN
```
