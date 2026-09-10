---
title: "if not Insert then Modify"
tags: ["AL","Performance"]
categories: ["Best Practice"]
---

_Created by SosicStefan, Described by SosicStefan_

## Introduction

When handling data insertion in Business Central, a common but inefficient pattern is attempting to insert a record and modifying it if the insert fails due to duplicate keys. This approach introduces unnecessary performance overhead and should be avoided.

{{% alert title="Note" color="info" %}}
This pattern is only good when used on singleton table. Read more about [Singleton Table](https://alguidelines.dev/docs/navpatterns/patterns/singleton/singleton-table/).
{{% /alert %}}

## Why Not Use 'If Not Insert Then Modify'?

Using the following structure:

```al
InitializeRecord(SomeRecord);
if not SomeRecord.Insert() then
    SomeRecord.Modify();
```

Presents several issues:

- Prevents buffered inserts: This impacts performance as batch insert optimizations are lost.
- Generates unnecessary SQL errors: If a duplicate primary key exists, the failed insert raises an error, which Business Central must handle.
- Slower and less efficient: Handling SQL errors is far from an optimal way to check for record existence.


## Best Practice: Use IsEmpty

Instead of relying on insert failures, ensure the record’s uniqueness upfront using a number series or an incremental primary key. If the data comes from an external source where duplicates might exist, use IsEmpty to check for existence.

```AL
InitializeRecord(SomeRecord);
SomeRecord.SetRecFilter();

if SomeRecord.IsEmpty() then
    SomeRecord.Insert()
else
    SomeRecord.Modify();
```

## Why IsEmpty is Better

- No SQL errors: The check is done before attempting an insert.
- Performance improvement: Avoids unnecessary error handling and maintains buffered inserts.
- More readable and efficient: The intent is clear—checking existence before performing actions.

## Summary
Avoid using if not Insert then Modify. Instead, ensure record uniqueness upfront or use IsEmpty to check existence efficiently. This leads to better performance and cleaner code.