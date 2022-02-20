---
title: "begin as an afterword"
tags: ["Readability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by waldo_

## Description

When `begin` follows `then`, `else`, `do`, it should be on the same line, preceded by one space character. 

## Bad code

```al
    if ICPartnerRefType = ICPartnerRefType::"Common Item No." then
    begin
    ...
    end;
```

## Good code

```al
    if ICPartnerRefType = ICPartnerRefType::"Common Item No." then begin
    ...
    end;
```

## [Discussions](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices?discussions_q=begin+as+an+After+Word+category%3A%22BC+Best+Practices%22)

You can find discussions on all "Best Practices" [here](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices).

If you don't find the discussion of this guideline, please feel free to create a new one with the same title as this article.  