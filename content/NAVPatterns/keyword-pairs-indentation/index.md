+++
title = "keyword-pairs-indentation.md"
weight = 730
+++
The IF..THEN pair, WHILE..DO pair, and FOR..DO pair must appear on the same line or the same level of indentation. Bad code

    IF (x = y) AND
    (a = b) THEN

Good code

    IF (x = y) AND
    (a = b) 
    THEN
