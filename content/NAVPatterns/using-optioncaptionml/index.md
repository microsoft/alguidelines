+++
title = "using-optioncaptionml.md"
weight = 1380
+++
The OptionCaptionML should be filled in for sourceexpression using option data types.

Bad code

    { 30 ;TextBox ;17850;0 ;150 ;423 ;Name=Selection;
    SourceExpr=Selection;
    DataSetFieldName=Selection }
    ...
    Selection@1008 : 'Open,Closed,Open and Closed';
    ...

Good code

    { 30 ;TextBox ;17850;0 ;150 ;423 ;Name=Selection;
    OptionCaptionML=ENU=Open,Closed,Open and Closed;
    SourceExpr=Selection;
    DataSetFieldName=Selection }
    ...
    Selection@1008 : 'Open,Closed,Open and Closed';
