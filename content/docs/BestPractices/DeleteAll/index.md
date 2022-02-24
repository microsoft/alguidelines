---
title: "DeleteAll"
tags: ["AL","Performance"]
categories: ["Best Practice"]
---

_Created by waldo, Described by waldo_

## Description

When you perform a "DeleteAll" when there is nothing to delete, it will still perform a lock.  When you for example perform a DeleteAll on an empty table, it will result in a table lock.
Therefore it's good practice to always check if the table is empty when performing a DeleteAll.

## Bad code

```al
    EmptyTableWLD.SetRange(Code, 'AJ');
    EmptyTableWLD.DeleteAll(true);
```

## Good code

```al
    EmptyTableWLD.SetRange(Code, 'AJ');
    if not EmptyTableWLD.IsEmpty() then
        EmptyTableWLD.DeleteAll(true);
```

## Discussions

You can discuss the guideline [here](https://github.com/microsoft/alguidelines/discussions/107)

You can find discussions on all "Best Practices" [here](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices).

If you don't find the discussion of this guideline, please feel free to create a new one with the same title as this article.  