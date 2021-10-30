+++
title = "Actions - Images"
weight = 200
+++
All actions must have an image assigned to them.
Bad code

    { 7 ;1 ;Action ;
    CaptionML=ENU=Customer - &Balance;
    RunObject=Report 121 }

Good code

    { 7 ;1 ;Action ;
    CaptionML=ENU=Customer - &Balance;
    RunObject=Report 121 }
    Image=Report }
