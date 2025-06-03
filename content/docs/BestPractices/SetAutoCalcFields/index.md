---
title: "SetAutoCalcFields"
tags: ["AL","Performance"]
categories: ["Best Practice"]
---

_Created by SosicStefan, Described by SosicStefan_

In Business Central, `SetAutoCalcFields` is an essential method for optimizing the retrieval of calculated fields, such as FlowFields. Without `SetAutoCalcFields`, FlowFields require explicit calculation via `CalcFields`, which adds performance overhead. By automatically computing FlowFields upon retrieval, `SetAutoCalcFields` reduces unnecessary calls and improves efficiency when reading FlowFields from multiple records in a loop.

See the documentation on learn.microsoft.com for more information about [SetAutoCalcFields](https://learn.microsoft.com/en-us/dynamics365/business-central/dev-itpro/developer/methods-auto/record/record-setautocalcfields-method).

{{% alert title="Note" color="info" %}}
Starting in Business Central 2025 release wave 1, the SetAutoCalcFields is also available on the RecordRef data type.
{{% /alert %}}

## Why Use SetAutoCalcFields?

Using `SetAutoCalcFields` ensures that FlowFields are computed automatically when retrieving a record, eliminating the need for additional `CalcFields` calls for each record.

## General Guidelines for SetAutoCalcFields

- Use `SetAutoCalcFields` before retrieving records (`FindSet`, `Find('-')`, etc.) when FlowFields need to be accessed.
- Avoid redundant `CalcFields` calls if `SetAutoCalcFields` has already been used.

## SetAutoCalcFields on List Pages
SetAutoCalcFields can be utilized in the OnOpenPage trigger to ensure specific FlowFields are pre-calculated before the user interacts with the page. This is particularly beneficial for list pages where calculations are necessary but not included in the visible fields. However, it's important to note that any FlowField already displayed on the page will be calculated automatically, making SetAutoCalcFields unnecessary for those fields.

## SetAutoCalcFields or CalcFields
It's best practice to use SetAutoCalcFields when fetching a record since it ensures automatic FlowField calculation. On the other hand, CalcFields should only be used if you already have the record and need to explicitly trigger the calculation. This distinction prevents unnecessary performance overhead, ensuring that database queries remain optimized.


## Examples
### Bad Code

```AL
Customer.SetFilter(Balance, '>%1' , LargeCredit);   
if Customer.FindSet() then 
    repeat  
        Customer.CalcFields(Balance);  
        if Customer.Balance > MaxCreditLimit then begin   
            Customer.Blocked := true;   
            Customer.Modify();   
        end else 
            if Customer.Balance > LargeCredit then begin   
                Customer.Caution := true;   
                Customer.Modify();   
            end;   
    until Customer.Next() = 0;
```

In this example, an extra call to `CalcFields` still must be issued for the code to be able to check the value of `Customer.Balance`. You can optimize this further by using the new `SetAutoCalcFields` method.

### Good code

```AL
Customer.SetFilter(Balance, '>%1', LargeCredit);   
Customer.SetAutoCalcFields(Balance)   
if Customer.FindSet() then 
    repeat   
        if Customer.Balance > MaxCreditLimit then begin   
            Customer.Blocked := true;   
            Customer.Modify();   
        end else 
            if Customer.Balance > LargeCredit then begin   
                Customer.Caution := true;   
                Customer.Modify();   
            end;   
    until Customer.Next() = 0;
```


