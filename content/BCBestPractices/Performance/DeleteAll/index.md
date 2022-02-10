+++
title = "DeleteAll"
weight = 1180
+++
This is a guideline, some parts are optional (if there's no content, remove the whole paragraph).

<_Created by waldo, Described by waldo_\>

## Description

When you perform a "DeleteAll" when there is nothing to delete, it will still perform a lock.  When you for example perform a DeleteAll on an empty table, it will result in a table lock.
Therefore it's good practice to always check if the table is empty when performing a DeleteAll.

## Bad code

```al
    EmptyTableWLD.Setrange(Code, 'AJ');
    EmptyTableWLD.DeleteAll(true);
```

## Good code

```al
    EmptyTableWLD.Setrange(Code, 'AJ');
    if not EmptyTableWLD.IsEmpty() then
        EmptyTableWLD.DeleteAll(true);
```

## [Discussions](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices?discussions_q=DeleteAll+category%3A%22BC+Best+Practices%22)

You can find discussions on all "Best Practices" [here](https://github.com/microsoft/alguidelines/discussions/categories/bc-best-practices).

If you don't find the discussion of this guideline, please feel fee to create a new one with the same title as this article.  