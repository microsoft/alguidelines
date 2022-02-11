+++
title = "Class Coupling"
weight = 320
+++
Do not write functions that have high class coupling. This makes the code hard to maintain.

Bad code

    Any procedure / trigger that has class coupling of > 30  
      
    

Good code

    Any procedure / trigger that has class coupling of <= 30\.
    Class coupling is computed by summing the unique instances of the following in a code block:
    - every unique usage of a complex C/AL data type (table, codeunit, etc) as 1\.
    - every unique usage of a DotNet type as 1\.
