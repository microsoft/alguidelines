+++
title = "Cyclomatic Complexity"
weight = 460
tags = ["C/AL"]
categories = ["Best Practice"]
+++
Do not write functions that have high cyclomatic complexity. This makes the code hard to maintain.

Bad code

    Any procedure / trigger that has a cyclomatic complexity > 25, using the CC3 version mentioned in [this article][anchor0].  
      
    

Good code

    Any procedure / trigger that has a cyclomatic complexity <= 25, using the CC3 version.
    The CC3 version is computed by summing the following in a code block:
    - each IF statement as 1\.
    - each entire CASE as 1\.



[anchor0]: http://www.aivosto.com/project/help/pm-complexity.html
