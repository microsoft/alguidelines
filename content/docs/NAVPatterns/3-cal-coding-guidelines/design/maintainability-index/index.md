+++
title = "Maintainability Index"
weight = 770
+++
[Maintainability Index][anchor0]: Do not write functions that have a very low maintainability index. This makes the code hard to maintain.

Bad code

    Any procedure / trigger that has a maintainability index < 20  
      
    

Good code

    Any procedure / trigger that has a maintainability index >= 20\.
    The Maintainability Index is computed as a function:
        - Lines Of Code (inverse proportional)
        - the Halstead Volume
        - Cyclomatic Complexity (inverse proportional).

More info

* [Halstead Volume][anchor1]
* [Cyclomatic Complexity][anchor2]

Bad code

    Any procedure / trigger that is > 100 lines of code  
      
    

Good code

    Any procedure / trigger that is <= 100 lines of code.
    A full C/AL Statement counts as 1 line of code



[anchor0]: http://blogs.msdn.com/b/codeanalysis/archive/2007/11/20/maintainability-index-range-and-meaning.aspx
[anchor1]: http://en.wikipedia.org/wiki/Halstead_complexity_measures
[anchor2]: http://www.aivosto.com/project/help/pm-complexity.html
