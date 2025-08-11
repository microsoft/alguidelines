+++
title = "Separate IF and ELSE"
weight = 1050
tags = ["C/AL","Readability"]
categories = ["Best Practice"]
+++
IF and ELSE statements should be on separate lines.

Bad code

```al
IF Atom[i+1] = '>' THEN HasLogicalOperator := TRUE ELSE BEGIN
    ...
END;
```

Good code

```al
IF Atom[i+1] = '>' THEN
    HasLogicalOperator := TRUE
ELSE BEGIN
    ...
END;
```
