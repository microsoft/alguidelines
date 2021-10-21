+++
title = "global-text-constants.md"
weight = 610
+++
Declare Text Constant as global variables.

Bad code

    PROCEDURE GetRequirementText@6(...) : Text\[50\];
    VAR
    RequirementOptionsTxt@1002 : TextConst 'ENU=Shipment,Receive,Pick,Put-Away';
    BEGIN

Good code

    VAR
    RequirementOptionsTxt@1002 : TextConst 'ENU=Shipment,Receive,Pick,Put-Away';
    ...
    PROCEDURE GetRequirementText@6(...) : Text\[50\];
    BEGIN
