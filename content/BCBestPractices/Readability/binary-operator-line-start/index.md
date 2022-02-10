+++
title = "Binary Operator to Start Line"
weight = 250
+++

<_Created by Microsoft, Described by waldo_\>

## Description

Do not start a line with a binary operator. 

## Bad code

```AL
"Quantity to Ship" :=
Quantity 
- "Quantity Shipped"
```

## Good code

```AL
"Quantity to Ship" :=
Quantity -
"Quantity Shipped"
```

## [Discussions](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices?discussions_q=binary+operator+to+start+line+category%3A%22BC+Best+Practices%22)

You can find discussions on all "Best Practices" [here](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices).

If you don't find the discussion of this guideline, please feel free to create a new one with the same title as this article.  
