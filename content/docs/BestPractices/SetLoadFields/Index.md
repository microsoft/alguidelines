---
title: "SetLoadFields"
tags: ["AL","Readability"]
categories: ["Best Practice"]
---

See the documentation on learn.microsoft.com for more information about [SetLoadFields](https://learn.microsoft.com/en-us/dynamics365/business-central/dev-itpro/developer/methods-auto/record/record-setloadfields-method).

For the performance of your code it is important that you use SetLoadFields as much as possible. 

If you want to retrieve a record from the database to check if the record is available always use SetLoadFields on the primary key fields of the table so only those fields will be retrieved from the database.

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


Place the SetLoadFields in the code before the line of the Get (or find). (there is no need to record filter fields in the SetLoadFields because these will be retrieved automatically).
## Bad code

```AL
Item.SetLoadFields("Item Category Code");
Item.SetRange("Third Party Item Exists", false);
Item.Get(ItemNo);
```

## Good code

```AL
Item.SetRange("Third Party Item Exists", false);
Item.SetLoadFields("Item Category Code");
Item.Get(ItemNo);
```

Place the SetLoadFields in the code before the case statement
## Bad code

```AL
Item.SetLoadFields("Item Category Code");
ItemCategoryCode := FindItemCategoryCode;

case true of
    Item.Get(ItemNo):
        SetItemCategoryCode(Item, ItemCategoryCode);
end;
```

## Good code

```AL
ItemCategoryCode := FindItemCategoryCode;
Item.SetLoadFields("Item Category Code");

case true of
    Item.Get(ItemNo):
        SetItemCategoryCode(Item, ItemCategoryCode);
end;
```
