+++
title = "Colon usage in CASE"
weight = 340
tags = ["C/AL","Readability"]
categories = ["Best Practice"]
+++
The last possibility on a CASE statement must be immediately followed by a colon.

Bad code

```al
CASE DimOption OF
    DimOption::"Global Dimension 1" :
        DimValue."Dimension Code" := GLSetup."Global Dimension 1 Code";
```

Good code

```al
CASE DimOption OF
    DimOption::"Global Dimension 1":
        DimValue."Dimension Code" := GLSetup."Global Dimension 1 Code";
```
