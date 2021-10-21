+++
title = "comments-curly-brackets.md"
weight = 350
+++
Never use curly bracket comments. During development, the "Block comment" functionality can be used instead. However, in production code, block comments are not recommended. Bad code

    PeriodTxt: {Period}  
      
    

Good code

    PeriodTxt: // Period  
      
    

Bad code

    PROCEDURE MATRIX\_OnAfterGetRecord@10(MATRIX\_ColumnOrdinal : Integer);
    BEGIN
    {
    IF ShowColumnName THEN
    MatrixHeader := MatrixRecords\[MATRIX\_ColumnOrdinal\].Name
    ELSE
    MatrixHeader := MatrixRecords\[MATRIX\_ColumnOrdinal\].Code;
    }
    MatrixRecord := MatrixRecords\[MATRIX\_ColumnOrdinal\];
    AnalysisValue := CalcAmt(ValueType,TRUE);
    MATRIX\_CellData\[MATRIX\_ColumnOrdinal\] := AnalysisValue;
    END;

Good code

    PROCEDURE MATRIX\_OnAfterGetRecord@10(MATRIX\_ColumnOrdinal : Integer);
    BEGIN
    MatrixRecord := MatrixRecords\[MATRIX\_ColumnOrdinal\];
    AnalysisValue := CalcAmt(ValueType,TRUE);
    MATRIX\_CellData\[MATRIX\_ColumnOrdinal\] := AnalysisValue;
    END;
