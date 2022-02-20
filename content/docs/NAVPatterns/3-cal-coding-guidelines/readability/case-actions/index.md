+++
title = "CASE Action"
weight = 310
+++
A CASE action should start on a line after the possibility.

Bad code

```al
CASE Letter OF
    'A': Letter2 := '10';
    'B': Letter2 := '11';
```

Good code

```al
CASE Letter OF
    'A':
        Letter2 := '10';
    'B':
        Letter2 := '11';
```