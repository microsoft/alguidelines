+++
title = "Colon usage in CASE"
weight = 340
+++
The last possibility on a CASE statement must be immediately followed by a colon. Bad code

    CASE DimOption OF
    DimOption::"Global Dimension 1" :
    DimValue."Dimension Code" := GLSetup."Global Dimension 1 Code";

Good code

    CASE DimOption OF
    DimOption::"Global Dimension 1":
    DimValue."Dimension Code" := GLSetup."Global Dimension 1 Code";
