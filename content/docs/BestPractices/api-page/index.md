---
title: "API Page / Query"
tags: ["AL","API"]
categories: ["Best Practice"]
---

_Created by Arend-Jan Kauffmann, Described by Arend-Jan Kauffmann_

## Description

API pages are different from UI pages. They require different properties and don't behave the same. Because API pages are used for integration with external applications, they should be treated as contracts. To achieve this, the following topics are important.

- Separate API app
- Page properties
- Versioning
- Field properties
- Default fields

## Separate API app

It's a good practice to develop API pages in a separate app instead of combining them in a solution. By doing so, it provides better maintainability and is a good way of separation of concerns.

## Page properties
An API page must define a minimum set of properties. Some of these properties will be part of the URL of the API endpoint. It is recommended to define the properties in the same order as they appear in the URL.

The properties that must be defined are:

- PageType = API / QueryType = API
- APIPublisher
- APIGroup
- APIVersion
- EntitySetName
- EntityName
- DelayedInsert (only Page)
- ODataKeyFields

### APIPublisher
The name of the API publisher is usually the company creating the API. It is the first custom part in the URL for a given endpoint. The value is case insensitive.

Example:

```al
APIPublisher = 'contoso';
```

### APIGroup
Sets the group of the API endpoint that page or query is exposed in. In the URL the APIGroup comes after the APIPublisher. It can be used to distinguish different API apps or groups of APIs from each other. The value is case insensitive.

Example:

```al
APIGroup = 'app1';
```

### APIVersion
Sets the version(s) of the API endpoint the page or query is exposed in. This property is not mandatory. If it is not specified, then APIs will be exposed as version 'beta'.

The APIVersion can be set to 'beta' or have the format 'vx.y'.
Example:

```al
APIVersion = 'beta';
```

or

```al
APIVersion = 'v1.0';
```
#### _Multiple API versions_
You should __never__ break existing versions. Any breaking change requires to create a new version.

It is possible to expose an API in multiple versions:
```al
APIVersion = 'beta', 'v1.0';
```
This allows to publish a new version of an API app without copying all individual objects and updating the version numbers. Only those API objects that are changed in a new version need to be copied. The other objects only need an addition to the APIVersion property to become available in the new version endpoint.

### EntitySetName
The EntitySetName is the plural entity name. Think of it as the name of the collection of entities. It is recommended to use camelCasing for this property. The value is case sensitive!

Example:

```al
EntitySetName = 'itemCategories';
```

### EntityName
The EntityName sets the singular entity name for the API page or query. This name is not used in the URL. Instead, the EntityName is used in the metadata information. It is recommended to use camelCasing for this property.

Example:

```al
EntityName = 'itemCategory';
```

### DelayedInsert
This property is required on an editable API page. It does not apply to an API query object. If ```Editable = false``` is set on the API page, then DelayedInsert is not required.
All APIs pages apply the behavior to first specify all field values and then insert the record at once.

Example:

```al
DelayedInsert = true;
```

### Full example
Together, the page properties look like:
```al
PageType = API;
APIPublisher = 'contoso';
APIGroup = 'app1';
APIVersion = 'v1.0';
EntitySetName = 'itemCategories';
EntityName = 'itemCategory';
DelayedInsert = true;
```

The full url will look like: ```https://{url}/api/contoso/app1/v1.0/companies({id})/itemCategories```

### ODataKeyFields
The ```EntitySetName``` property in the URL can be extended with an identifier to indicate a single record.

Example:
```
.../itemCategories(768b6173-9b19-40ea-8e5d-ce181ec0d645)
```

The property ```ODataKeyFields``` defines which field(s) will be used for the identifier value. It is highly recommended to always use the SystemId field for this property. The SystemId field is immutable and will never change for a record.

The field that is defined in this property should be part of the API page.

## Field properties
The base structure of an API page is similar to a UI list page:

```al
layout
{
    area(Content)
    {
        repeater(records)
        {
            ...
        }
    }
}
```

When specifying the fields there are some considerations to keep in mind.

```
field(displayName; Rec.Name) { }
```

There are no mandatory properties. The property ```ApplicationArea``` does not play a role in API pages, so it can be skipped. The property ```Caption``` is also optional and should only be used in case the external application requires captions and the caption should be different from the standard caption as defined in the table.

The name of the field, in the example above ```displayName```, should be defined in camelCasing. It may not contain spaces, dots, or other special characters.

It is common use to give certain fields a more describing name. Some examples are:

* id for field SystemId
* number for field "No."
* displayName for field Name

## Default fields
An API should at least expose these fields:

* SystemId
  * This field should be exposed wit the name ```id```
* SystemModifiedAt
  * This field should be exposed with the name ```lastModifiedDateTime```. If you choose a different name, then the webhook functionality may not work properly.

Example:

```al
layout
{
    area(Content)
    {
        repeater(records)
        {
            field(id; SystemId) { }
            field(lastModifiedDateTime; SystemModifiedAt) { }
        }
    }
}
```
