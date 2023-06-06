---
title: "SetLoadFields"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

See the documentation on learn.microsoft.com for more information about [SetLoadFields](https://learn.microsoft.com/en-us/dynamics365/business-central/dev-itpro/developer/methods-auto/record/record-setloadfields-method).

For the performance of your code it is important that you use SetLoadFields as much as possible. 

If you want to retrieve a record from the database to check if the record is available the advice is to set an SetLoadFields on the primary key fields of the table so only those fields will be retrieved from the database.

## Bad code

```AL
if not Item.Get(ItemNo) then 
    exit();
```

## Good code

```AL
Item.SetLoadFields("No.");
if not Item.Get(ItemNo) then
    exit();
```

Place the SetLoadFields in the code before filtering the record (there is no need to record filterfields in the SetLoadFields).
## Bad code

```AL
Item.Setrange("Third Party Item Exists", false);
Item.SetLoadFields("Item Category Code");
Item.Get(ItemNo);
```

## Good code

```AL
Item.SetLoadFields("Item Category Code");
Item.Setrange("Third Party Item Exists", false);
Item.Get(ItemNo);
```

