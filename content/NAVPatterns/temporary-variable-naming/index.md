+++
title = "temporary-variable-naming.md"
weight = 1200
+++
The name of a temporary variable must be prefixed with the word Temp and not otherwise. Bad code

    JobWIPBuffer@1002 : TEMPORARY Record 1018;   
      
    

Good code

    TempJobWIPBuffer@1002 : TEMPORARY Record 1018;   
    

Bad code

    TempJobWIPBuffer@1002 : Record 1018;   
      
    

Good code

    CopyOfJobWIPBuffer@1002 : Record 1018;
