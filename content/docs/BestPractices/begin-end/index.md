---
title: "Begin-End - Compound Only"
tags: ["AL","Readability"]
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
