+++
title = "Unnecessary true/false"
weight = 1300
+++

<_Created by Microsoft, Described by waldo_\>

## Description
Do not use `true` or `false` keywords unnecessarily if the expression is already an logical expression.

## Bad code

```al
    if IsPositive() = true then  
```      
    
## Good code

```al
    if IsPositive() then  
```      
    

## Bad code

```al
    if Complete <> true then  
```      
    
## Good code

```al
    if not Complete then
```

## [Discussions](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices?discussions_q=unnecessary+true+false+category%3A%22BC+Best+Practices%22)

You can find discussions on all "Best Practices" [here](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices).

If you don't find the discussion of this guideline, please feel free to create a new one with the same title as this article.  