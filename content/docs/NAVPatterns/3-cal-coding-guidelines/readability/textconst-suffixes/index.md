+++
title = "TextConst Suffixes"
weight = 1210
+++
TextConst variable names should have a suffix (an approved three-letter suffix: Msg, Tok, Err, Qst, Lbl, Txt) describing usage.

Bad code

```al
CannotDeleteLine@1005 : TextConst 'ENU=You cannot delete this line because one or more rating values exists.';
...
ERROR(CannotDeleteLine,TABLECAPTION);
```

Good code

```al
CannotDeleteLineErr@1005 : TextConst 'ENU=You cannot delete this line because one or more rating values exists.';
...
ERROR(CannotDeleteLineErr,TABLECAPTION); 
```

Bad code

```al
Text000@1011 : TextConst 'ENU="has been changed (initial a %1: %2= %3, %4= %5)"';
...
SalesLine.FIELDERROR(Type,STRSUBSTNO(Text000,...);
...
```

Good code

```al
TypeHasBeenChangedErr@1011 : TextConst 'ENU="has been changed (initial a %1: %2= %3, %4= %5)"';
...
SalesLine.FIELDERROR(Type,STRSUBSTNO(TypeHasBeenChangedErr,...);
... 
```

Bad code

```al
Text004@1004 : TextConst 'ENU=Indenting the Job Tasks \#1\#\#\#\#\#\#\#\#\#\#.';
...
Window@1007 : Dialog;
...
    Window.OPEN(Text004);
```

Good code

```al
IndentingMsg@1004 : TextConst 'ENU=Indenting the Job Tasks \#1\#\#\#\#\#\#\#\#\#\#.';
...
Window@1007 : Dialog;
...
    Window.OPEN(IndentingMsg);
```

Bad code

```al
Text002@1005 : TextConst 'ENU=You cannot delete a %1 that is used in one or more setup windows.\\ Do you want to open the G/L Account No. Where-Used List Window?';
...
IF CONFIRM(Text002,TRUE,GLAcc.TABLECAPTION) THEN
```

Good code

```al
OpenWhereUsedWindowQst@1005 : TextConst 'ENU=You cannot delete a %1 that is used in one or more setup windows.\\ Do you want to open the G/L Account No. Where-Used List Window?';
...
IF CONFIRM(OpenWhereUsedWindowQst,TRUE,GLAcc.TABLECAPTION) THEN
```

Bad code

```al
Selection := STRMENU(Text003,2);
...
Text003@1002 : TextConst 'ENU=&Copy dimensions from BOM,&Retrieve dimensions from components';
```

Good code

```al
    Selection := STRMENU(CopyFromQst,2);
    ...
    CopyFromQst@1002 : TextConst 'ENU=&Copy dimensions from BOM,&Retrieve dimensions from components';
```

Bad code

```al
DATASET
{
...
    { 1 ;1 ;Column ;Chart_of_AccountsCaption;
                    SourceExpr=Chart_of_AccountsCaption }
...
Chart_of_AccountsCaption@9647 : TextConst 'ENU=Chart of Accounts';
```

Good code

```al
DATASET
{
...
    { 1 ;1 ;Column ;Chart_of_AccountsCaption;
                    SourceExpr=ChartOfAccountsLbl }
...
ChartOfAccountsLbl@9647 : TextConst 'ENU=Chart of Accounts';
```
