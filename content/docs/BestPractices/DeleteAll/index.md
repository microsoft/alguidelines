---
title: "DeleteAll"
tags: ["AL","Performance"]
categories: ["Best Practice"]
---

_Created by waldo, Described by waldo_

## Description

When you perform a "DeleteAll" when there is nothing to delete, it will still perform a lock.  When you for example perform a DeleteAll on an empty table, it will result in a table lock.
Therefore it's good practice to always check if the table is empty when performing a DeleteAll.

## <span style="color:FireBrick">Bad code</span>

```al
    EmptyTableWLD.SetRange(Code, 'AJ');
    EmptyTableWLD.DeleteAll(true);
```

## <span style="color:ForestGreen">Good code</span>

```al
    EmptyTableWLD.SetRange(Code, 'AJ');
    if not EmptyTableWLD.IsEmpty() then
        EmptyTableWLD.DeleteAll(true);
```
