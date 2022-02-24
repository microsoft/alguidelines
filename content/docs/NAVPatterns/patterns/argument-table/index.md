+++
title = "Argument Table"
weight = 220
tags = ["C/AL"]
categories = ["Pattern"]
+++
_Originally By Nikola Kukrika and waldo_

### Abstract

The Argument Table pattern is used to provide an extension point for adding new arguments without changing the signature. By grouping multiple arguments into a table the code becomes more readable (function signature and the usage of the function).

[![ ][image0]][anchor0]

### Problem

In CAL overloading function signature is not supported. It is also not possible to provide default values for the function arguments.

When an argument needs to be added to the function, the existing function needs to be extracted to a new method with an additional argument and the original function will call new method. This will cause an upgrade problem in the future, since the entire body of the method is replaced.

Second commonly occurring problem is option duplication. In order to pass options often they are duplicated in the signature.

The last problem that can be solved is high number of arguments. Functions with a high number of arguments are hard to understand. Having arguments grouped within the table with a meaningful name will improve readability and make code easier to understanding.

Few examples of the bad implementations are as illustrated here:

#### Bad example 1

  
```AL
PROCEDURE FillInVATReturnData@1200001(VAR DeclarationID@1200000 : Code [20];VAR LineID@1200001 : Code [20];VAR PeerID@1200002 : Code [20]; VAR DocumentNo@1200003: Code[20]; VAR NumberOfCopies@1200007: Integer; VAR Uploaded@1200004 : Boolean; VAR Correction@1200005 : Boolean; VAR HasValidationErr@1200006 : Boolean);
```

**Call**


```AL
FillInVATReturnData(NoSeries, NextLineID, CustomerID, DocumentNo, SingleCopy, ???, ??, ...., ...)
```

In this example the code is hard to read and understand. Adding an additional argument will require refactoring of the existing function. Each time a new argument is added a new function will be created.

#### Bad example 2

```AL
LOCAL PROCEDURE GetTableSyncSetupW1@3(OldTableId@1002 : Integer; VAR UpgradeTableId@1001 : Integer; VAR TableUpgradeMode@1000 : 'Check, Copy, Move, Force') : Boolean;  
BEGIN  
    CASE OldTableId OF  
        DATABASE::"Sales Header":  
            SetTableSyncSetup(0,TableUpgradeMode::Check,UpgradeTableId,TableUpgradeMode);  
        DATABASE::"Posting Exch. Column Def":  
            SetTableSyncSetup(104025,TableUpgradeMode::Copy,UpgradeTableId,TableUpgradeMode);  
        DATABASE::"Payment Export Data":  
            SetTableSyncSetup(0,TableUpgradeMode::Force,UpgradeTableId,TableUpgradeMode);  
        ELSE  
            EXIT(FALSE);  
    END;  
    EXIT(TRUE);  
END;
```

In this example each time a new argument is added all function calls will have to be updated. Option is duplicated in the signature, which will cause issues if a new option is defined or the existing options are renamed.

### Solution

By grouping the arguments within the table it is possible to add additional argument and reuse it where it is needed without changing the signature.

Multiple parameters are grouped within the single object with a meaningful name so the code becomes more readable.

It is possible to assign default values and to have the code validation.

Argument table should preferably be a temporary table since the implementation is simpler.

The examples of usages addressing problems shown above are:

#### Good example 1

New table  
```AL
TAB 50003 VAT Return Data  
PROCEDURE FillInVATReturnData@1200001(VAR VATReturnData@1200000 : Record 50003);

VATReturnData.INIT;  
VATReturnData.NumberOfCopies := GetDefaultNumberOfCopies;  
VATReturnData.Uploaded := FALSE;

FillInVATReturnData(VATReturnData);
```

By introducing an argument table, code is much more readable since there is a single argument for a function. It is easy to see which arguments are passed in and which are modified in a function.

#### Good example 2

Good example  
```AL
PROCEDURE GetTableSyncSetupW1@3(VAR TableSynchSetup@1000 : Record 2000000135); 
BEGIN  
    SetTableSyncSetup(DATABASE::"Sales Header",0,TableSynchSetup.Mode::Check);  
    SetTableSyncSetup(DATABASE::"Posting Exch. Column Def",104025,TableSynchSetup.Mode::Copy);  
    SetTableSyncSetup(DATABASE::"Payment Export Data",0,TableSynchSetup.Mode::Force);  
END;
```

Option definition is not encapsulated within the table. Arguments are grouped and we can add additional arguments without the need to change the signature.

### Downsides

You need to create one more table

Complex types can't be embedded as fields in tables (cannot have a record field type etc).

### NAV Usages

Upgrade Codeunits

### Related Patterns

Posting Routine, Select behavior: Setting fields on existing records in order not to change the signatures. 



[anchor0]: 0218.Argument-Table-image.png


[image0]: 0218.Argument-Table-image.png
