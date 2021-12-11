+++
title = "CASE Action on next line"
weight = 310
+++

<_Created by Microsoft, Described by waldo_\>

## Description

A CASE action should start on a line after the possibility. 

## Bad code

```AL
    case Letter of
        'A': Letter2 := '10';
        'B': Letter2 := '11';
    ...
```

## Good code

```AL
    case Letter of
        'A':
            Letter2 := '10';
        'B':
            Letter2 := '11';
    ...
```

## [Discussions](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices?discussions_q=case+action+on+next+line+category%3A%22BC+Best+Practices%22)

You can find discussions on all "Best Practices" [here](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices).

If you don't find the discussion of this guideline, please feel fee to create a new one with the same title as this article.  
