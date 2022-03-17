---
title: "Avoid too many blank lines"
tags: ["AL"]
categories: ["Best Practice"]
---

## Description

Avoid too many blank lines.

## Bad code

```al
var
    IsVisible: Boolean;
    IsEditable: Boolean;


local procedure Init()
begin
    IsVisible := false;
    IsEditable := false;


    exit(true);
end;
```

## Good code

```al
var
    IsVisible: Boolean;
    IsEditable: Boolean;

local procedure Init()
begin
    IsVisible := false;
    IsEditable := false;

    exit(true);
end;
```
