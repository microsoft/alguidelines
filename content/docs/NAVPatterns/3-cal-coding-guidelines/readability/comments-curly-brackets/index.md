+++
title = "Comments inside Curly Brackets"
weight = 350
tags = ["C/AL","Readability"]
categories = ["Best Practice"]
+++
Never use curly bracket comments. During development, the "Block comment" functionality can be used instead. However, in production code, block comments are not recommended.

Bad code

```al
PeriodTxt: {Period}  
``` 
    
Good code

```al
PeriodTxt: // Period  
``` 
    

Bad code

```al
PROCEDURE MATRIX_OnAfterGetRecord@10(MATRIX_ColumnOrdinal : Integer);
BEGIN
    {
    IF ShowColumnName THEN
    MatrixHeader := MatrixRecords[MATRIX_ColumnOrdinal].Name
    ELSE
    MatrixHeader := MatrixRecords[MATRIX_ColumnOrdinal].Code;
    }
    MatrixRecord := MatrixRecords[MATRIX_ColumnOrdinal];
    AnalysisValue := CalcAmt(ValueType,TRUE);
    MATRIX_CellData[MATRIX_ColumnOrdinal] := AnalysisValue;
END;
```

Good code

```al
PROCEDURE MATRIX_OnAfterGetRecord@10(MATRIX_ColumnOrdinal : Integer);
BEGIN
    MatrixRecord := MatrixRecords[MATRIX_ColumnOrdinal];
    AnalysisValue := CalcAmt(ValueType,TRUE);
    MATRIX_CellData[MATRIX_ColumnOrdinal] := AnalysisValue;
END;
```
