+++
title = "CaptionML on System Pages"
weight = 300
tags = ["C/AL"]
categories = ["Best Practice"]
+++
CaptionML should always be specified on a page field for a system table. By default, system tables do not have captions, so if you need to use them in the UI then captions need to be added.

Bad code

    ...
    { 2 ;2 ;Field ;
    SourceExpr=Name }
    ...
    OBJECT Table 2000000000 User
    ...
    { 2 ; ;Name ;Text50 }

Good code

    ...
    { 2 ;2 ;Field ;
    CaptionML=ENU=Name;
    SourceExpr=Name }
    ...
    OBJECT Table 2000000000 User
    ...
    { 2 ; ;Name ;Text50 }
