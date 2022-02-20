+++
title = "Static Object Invocation"
weight = 1160
+++
Call objects statically whenever possible. It reduces extra noise and removes extra variables. Downside: changing the name of the object which is called statically will need a code update. 

Bad code

    LOCAL PROCEDURE Code@1();
    VAR
        CAJnlPostBatch@1001 : Codeunit 1103;
    BEGIN
        CAJnlPostBatch.Run(CostJnlLine);
    END;

Good code

    LOCAL PROCEDURE Code@1();
    BEGIN
        CODEUNIT.RUN(CODEUNIT::"CA Jnl.-Post Batch",CostJnlLine);
    END;
