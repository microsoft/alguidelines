+++
title = "Keyword Pairs - Indentation"
weight = 730
tags = ["C/AL","Readability"]
categories = ["Best Practice"]
+++
The IF..THEN pair, WHILE..DO pair, and FOR..DO pair must appear on the same line or the same level of indentation.

Bad code

```al
IF (x = y) AND
   (a = b) THEN
```

Good code

```al
IF (x = y) AND
   (a = b) 
THEN
```
