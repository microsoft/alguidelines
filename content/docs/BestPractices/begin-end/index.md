---
title: "Begin-End - Compound Only"
tags: ["Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

Only use begin..end to enclose [compound statements](https://docs.microsoft.com/en-us/cpp/c-language/compound-statement-c?view=msvc-170#:~:text=A%20compound%20statement%20%28also%20called%20a%20%22block%22%29%20typically,appear%20at%20the%20head%20of%20a%20compound%20statement.).

## Bad code

```AL
if FindSet() then begin
    repeat
    ...
    until next() = 0;
end;
```

## Good code

```AL
if FindSet() then
    repeat
    ...
    until next() = 0;
```

## Bad code

```AL
if IsAssemblyOutputLine then begin
    TestField("Order Line No.", 0);
end;
```

## Good code

```AL
if IsAssemblyOutputLine then
    TestField("Order Line No.", 0);
```

## Exception

```AL
// Except for this case
if X then begin
    if Y then 
        //DO SOMETHING;
end else 
    (not X)
```

## [Discussions](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices?discussions_q=begin+end+compound+only+category%3A%22BC+Best+Practices%22)

You can find discussions on all "Best Practices" [here](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices).

If you don't find the discussion of this guideline, please feel free to create a new one with the same title as this article.  
