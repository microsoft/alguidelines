+++
title = "with-scope-name-collision.md"
weight = 1450
+++
Do not use the WITH scope when it has a variable whose name is the same as a local variable. This can lead to wrong code assumptions.

**Given that **"Contract Type" is a field on table ServiceContractHeader, then in the following example there is a parameter name clash with the field name. Which one will be used?

Bad code

    PROCEDURE InsertData@1("Contract Type"@1000 : Option...);
    ...
    BEGIN
    ...
    WITH ServiceContractHeader DO BEGIN
    ...
    DimMgt.InsertServContractDim(...,"Contract Type","Contract No.",0,...);
    END;

Good code

    PROCEDURE InsertData@1(ContractType@1000 : Option...);
    ...
    BEGIN
    ...
    WITH ServiceContractHeader DO BEGIN
    ...
    DimMgt.InsertServContractDim(...,ContractType,"Contract No.",0,...);
    END;
