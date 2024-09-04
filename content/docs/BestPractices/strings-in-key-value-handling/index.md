---
title: "Strings in key/value handling"
tags: ["AL","Readability", "Maintainability"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by Peter Conijn_

## Description
In some cases, notably when storing or retrieving values in key/value objects like dictionaries, XML or JSON objects, you will require a key value. In order to keep these tags maintainable, these key values should never be hard-coded, but should be stored in locked labels. Such locked labels should - as per convention - have the suffix -Tok.

## Example 1

In this example, we show the wrong and right way to store values into a JSON object. These examples or course also applies to other key/value types. Do note that in these examples, as per JSON guidlines, the keys are in camelCase rather than PascalCase.

### Bad code

```al
    local procedure AddValuesToJson(Customer: Record Customer) JObject: JsonObject
    begin
        JObject.Add('customerName', Customer.Name);
        JObject.Add('customerAddress', Customer.Address);
    end;
```

### Good code

```al
    var
        NameKeyTok: Label 'customerName', Locked = true;
        AddressKeyTok: Label 'customerAddress', Locked = true;

    local procedure AddValuesToJson(Customer: Record Customer) JObject: JsonObject
    begin
        JObject.Add(NameKeyTok, Customer.Name);
        JObject.Add(AddressKeyTok, Customer.Address);        
    end;
```

## Example 2

In this second  example, we show the wrong and right way to retrieve values from a JSON object.

### Bad code

```al
    local procedure ParseJsonObject(JObject: JsonObject)
    var
        Customer: Record Customer;
    begin
        Customer.Init();
        Customer.Name := GetJsonValueAsText(JObject, 'customerName');
        Customer.Address := GetJsonValueAsText(JObject, 'customerAddress');
        // ...More code...
    end;
```

### Good code

```al
    var
        NameKeyTok: Label 'customerName', Locked = true;
        AddressKeyTok: Label 'customerAddress', Locked = true;

    local procedure ParseJsonObject(JObject: JsonObject)
    var
        Customer: Record Customer;
    begin
        Customer.Init();
        Customer.Name := GetJsonValueAsText(JObject, NameKeyTok);
        Customer.Address := GetJsonValueAsText(JObject, AddressKeyTok);
        // ...More code...
    end;
```