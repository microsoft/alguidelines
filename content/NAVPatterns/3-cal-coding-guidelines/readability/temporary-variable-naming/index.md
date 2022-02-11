+++
title = "Temporary Variable Naming"
weight = 1200
+++
The name of a temporary variable must be prefixed with the word Temp and not otherwise.

Bad code

```al
JobWIPBuffer@1002 : TEMPORARY Record 1018;   
```
    

Good code

```al
TempJobWIPBuffer@1002 : TEMPORARY Record 1018;   
```

Bad code

```al
TempJobWIPBuffer@1002 : Record 1018;   
```
    

Good code

```al
CopyOfJobWIPBuffer@1002 : Record 1018;
```
