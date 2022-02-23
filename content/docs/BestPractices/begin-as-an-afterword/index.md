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

## Discussion

Discuss this Best Practice [here](https://github.com/microsoft/alguidelines/discussions/123)

You can find discussions on all "Best Practices" [here](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices).
