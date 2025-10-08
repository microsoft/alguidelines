---
title: "AL Upgrade Instructions"
description: >
  Guidelines for writing and handling upgrade code
globs: ["*.al"]
alwaysApply: false
---

## Overview
These instructions cover how to write and review Business Central AL upgrade code following best practices for performance, reliability, and maintainability.

## 1. Upgrade Codeunit Structure

### Basic Structure
All upgrade codeunits must follow this exact structure:

```al
codeunit [ID] [CodeunitName]
{
    Subtype = Upgrade;
    
    trigger OnCheckPreconditionsPerCompany()
    begin
        // Your code here
    end;

    trigger OnCheckPreconditionsPerDatabase()
    begin
        // Your code here
    end;

    trigger OnUpgradePerCompany()
    begin
        // Your code here
    end;

    trigger OnUpgradePerDatabase()
    begin
        // Your code here
    end;

    trigger OnValidateUpgradePerCompany()
    begin
        // Your code here
    end;

    trigger OnValidateUpgradePerDatabase()
    begin
        // Your code here
    end;
}
```

### Critical Rule: Avoid OnValidate and OnCheckPreconditions Triggers
- **DO NOT USE** `OnValidateUpgradePerCompany()` and `OnValidateUpgradePerDatabase()` triggers
- **DO NOT USE** `OnCheckPreconditionsPerCompany()` and `OnCheckPreconditionsPerDatabase()` triggers
- These triggers have performance impact and run on every upgrade
- If developer insists on using them:
  - They MUST provide written justification
  - Code MUST include checks to skip execution when upgrade is completed
  - Should use upgrade tags for these checks

## 2. OnUpgrade Trigger Implementation

### Rule: No Direct Code in Triggers
OnUpgrade triggers should only contain method calls, never direct implementation:

**INCORRECT Example:**
```al
trigger OnUpgradePerCompany()
begin
    // Direct implementation code here - WRONG!
    Customer.ModifyAll("Some Field", true);
end;
```

**CORRECT Example:**
```al
codeunit 4123 UpgradeMyFeature
{
    Subtype = Upgrade;
   
    trigger OnUpgradePerCompany()
    begin
        UpgradeMyFeature();
        UpgradeSecondFeature();
    end;

    local procedure UpgradeMyFeature()
    begin
        Customer.ModifyAll("Some Field", true);
        // Other upgrade code here
    end;

    local procedure UpgradeSecondFeature()
    begin
        // Your upgrade implementation here
    end;
}
```

## 3. Error Handling Philosophy

### Rule: Minimize Upgrade Blocking
- **Throw errors ONLY** when absolutely necessary to abort upgrade
- Handle unexpected scenarios gracefully without blocking. All read operations (Get, Find, FindSet, FindFirst, FindLast) should have if [OPERATION] then to make it safe.
**BAD EXAMPLE**
```al
Item.Get();
Customer.FindSet();
Vendor.FindLast();
```
**GOOD EXAMPLE**
```al
if Item.Get() then
    // CustomCode;
if Customer.FindSet() then;
if not Vendor.FindLast() then
   exit;
```

- Use telemetry for logging issues instead of throwing errors
- Customers should not be blocked from upgrading due to data inconsistencies

**Example:**
```al
// GOOD - Handle gracefully
if not Customer.Get(CustomerNo) then begin
    // Log telemetry about missing customer
    Session.LogMessage('0000ABC', 'Customer not found during upgrade', Verbosity::Warning, DataClassification::SystemMetadata);
    exit; // Continue with upgrade
end;

// BAD - Blocks upgrade
Customer.Get(CustomerNo); // Will throw error if not found
```

## 4. Database Operations Safety

### Rule: All Read Operations Must Be Protected
Every GET, FIND, FINDSET, FINDLAST operation MUST be within IF-THEN structure:

**CORRECT Examples:**
```al
if MyTable.Get(CustomerNo) then
    MyTable.Modify();

if MyTable.FindSet() then
    repeat
        // Process records
    until MyTable.Next() = 0;

if MyTable.FindLast() then
    // Process record
```

**INCORRECT Examples:**
```al
MyTable.Get(CustomerNo); // WRONG - not protected
MyTable.FindLast();   // WRONG - not protected
```

## 5. Execution Control - Use Upgrade Tags (Not Version Checks)

### AVOID Version Checks
**BAD Examples (Do Not Use):**
```al
// WRONG - Version check approach
if MyApplication.DataVersion().Major > 14 then 
    exit;

// WRONG - Complex version structure
if MyApplication.DataVersion().Major < 14 then
    UpgradeFeatureA()
else if MyApplicationDataVersion().Major < 17 then
    UpgradeFeatureB()
else
    exit;
```

### Valid Version Check Usage
**ONLY acceptable use** - checking for first installation:
```al
trigger OnInstallAppPerCompany()
var
    AppInfo: ModuleInfo;
begin
    NavApp.GetCurrentModuleInfo(AppInfo);
    if (AppInfo.DataVersion() <> Version.Create('0.0.0.0')) then
        exit;
    // Insert installation code here
end;

// Alternative approach
trigger OnInstallAppPerCompany()
var
    AppInfo: ModuleInfo;
begin
    if AppInfo.DataVersion().Major() = 0 then
        SetAllUpgradeTags();
    
    CompanyInitialize();
end;
```

### USE Upgrade Tags (Preferred Method)
**CORRECT Implementation:**
```al
local procedure UpgradeMyFeature()
var
    UpgradeTag: Codeunit "Upgrade Tag";
begin
    if UpgradeTag.HasUpgradeTag(MyUpgradeTag()) then
        exit;

    // Your upgrade code here

    UpgradeTag.SetUpgradeTag(MyUpgradeTag());
end;

// Register PerCompany tags
[EventSubscriber(ObjectType::Codeunit, Codeunit::"Upgrade Tag", 'OnGetPerCompanyUpgradeTags', '', false, false)]
local procedure RegisterPerCompanyTags(var PerCompanyUpgradeTags: List of [Code[250]])
begin
    PerCompanyUpgradeTags.Add(MyUpgradeTag());
end;

// Register PerDatabase tags
[EventSubscriber(ObjectType::Codeunit, Codeunit::"Upgrade Tag", 'OnGetPerDatabaseUpgradeTags', '', false, false)]
local procedure RegisterPerDatabaseTags(var PerDatabaseUpgradeTags: List of [Code[250]])
begin
    PerDatabaseUpgradeTags.Add(MyUpgradeTag());
end;
```

### Upgrade Tag Rules
- Maximum 2 levels of nesting in upgrade tag logic
- No complex if-then structures
- **IMPORTANT** When adding new lines to the register upgrade tags subscribers you must check from where the upgrade method is called. If it is called from OnUpgradePerCompany then it must be registered from OnGetPerCompanyUpgradeTags method. If it is called from OnUpgradePerDatabase it must be registered under OnGetPerDatabaseUpgradeTags. It **MUST** not be called from both, we need to use a different tags in this case.
- Reuse existing event subscribers when possible - only add new lines.
- Use upgrade tags ONLY in upgrade code
- Every new upgrade tag added **MUST** be referenced within an OnGetPerDatabaseUpgradeTags or OnGetPerCompanyUpgradeTags event subscriber 
## 6. No Outside Calls During Upgrade

### Rule: No Outside Calls During Upgrade
**FORBIDDEN during upgrade:**
- HttpClient or web service requests
- DotNet interop method calls
- Any external system communication

These operations can fail and block the upgrade process. If they succeed and the upgrade fails, it may not be possible to roll changes back. 

## 7. Execution Context Awareness

### Rule: Use Execution Context to Skip Code
It's acceptable to skip code execution during upgrade using ExecutionContext:

**CORRECT Example:**
```al
// Don't add report selection entries during upgrade
if GetExecutionContext() = ExecutionContext::Upgrade then
    exit;
```

**Requirements:**
- MUST include comment explaining why code is skipped
- Should be used sparingly and with clear justification

## 8. DataTransfer Usage for Performance

### When to Use DataTransfer
**MUST use DataTransfer when:**
- Table can contain more than 300,000 records
- Adding new fields to existing tables
- Adding new tables that need data initialization

**MUST use ONLY for:**
- New fields and tables added in the same PR
- Initializing newly added data structures

**IMPORTANT**
- If there is no new fields and tables, comment should be added that the validation triggers and event subscribers will not be raised, potentially breaking the business logic.
- If a new field is added, especially with InitValue, datatransfer is strongly recommended to be used to have a fast upgrade.

### DataTransfer vs Loop/Modify Comparison

**BAD Example (Loop/Modify - Avoid for Large Data):**
```al
local procedure UpdatePriceSourceGroupInPriceListLines()
var
    PriceListLine: Record "Price List Line";
    UpgradeTag: Codeunit "Upgrade Tag";
    UpgradeTagDefinitions: Codeunit "Upgrade Tag Definitions";
begin
    if UpgradeTag.HasUpgradeTag(UpgradeTagDefinitions.GetPriceSourceGroupUpgradeTag()) then
        exit;

    PriceListLine.SetRange("Source Group", "Price Source Group"::All);
    if PriceListLine.FindSet(true) then
        repeat
            if PriceListLine."Source Type" in
                ["Price Source Type"::"All Jobs",
                "Price Source Type"::Job,
                "Price Source Type"::"Job Task"]
            then
                PriceListLine."Source Group" := "Price Source Group"::Job
            else
                case PriceListLine."Price Type" of
                    "Price Type"::Purchase:
                        PriceListLine."Source Group" := "Price Source Group"::Vendor;
                    "Price Type"::Sale:
                        PriceListLine."Source Group" := "Price Source Group"::Customer;
                end;
            if PriceListLine."Source Group" <> "Price Source Group"::All then
                PriceListLine.Modify();
        until PriceListLine.Next() = 0;

    UpgradeTag.SetUpgradeTag(UpgradeTagDefinitions.GetPriceSourceGroupFixedUpgradeTag());
end;
```

**GOOD Example (DataTransfer - Use for Large Data):**
```al
local procedure UpdatePriceSourceGroupInPriceListLines()
var
    PriceListLine: Record "Price List Line";
    UpgradeTag: Codeunit "Upgrade Tag";
    UpgradeTagDefinitions: Codeunit "Upgrade Tag Definitions";
    PriceListLineDataTransfer: DataTransfer;
begin
    if UpgradeTag.HasUpgradeTag(UpgradeTagDefinitions.GetPriceSourceGroupUpgradeTag()) then
        exit;

    // Update Job-related records
    PriceListLineDataTransfer.SetTables(Database::"Price List Line", Database::"Price List Line");
    PriceListLineDataTransfer.AddSourceFilter(PriceListLine.FieldNo("Source Group"), '=%1', "Price Source Group"::All);
    PriceListLineDataTransfer.AddSourceFilter(PriceListLine.FieldNo("Source Type"), '%1|%2|%3', 
        "Price Source Type"::"All Jobs", "Price Source Type"::Job, "Price Source Type"::"Job Task");
    PriceListLineDataTransfer.AddConstantValue("Price Source Group"::Job, PriceListLine.FieldNo("Source Group"));
    PriceListLineDataTransfer.CopyFields();
    Clear(PriceListLineDataTransfer);

    // Update Vendor-related records
    PriceListLineDataTransfer.SetTables(Database::"Price List Line", Database::"Price List Line");
    PriceListLineDataTransfer.AddSourceFilter(PriceListLine.FieldNo("Source Group"), '=%1', "Price Source Group"::All);
    PriceListLineDataTransfer.AddSourceFilter(PriceListLine.FieldNo("Source Type"), '<>%1&<>%2&<>%3', 
        "Price Source Type"::"All Jobs", "Price Source Type"::Job, "Price Source Type"::"Job Task");
    PriceListLineDataTransfer.AddSourceFilter(PriceListLine.FieldNo("Price Type"), '=%1', "Price Type"::Purchase);
    PriceListLineDataTransfer.AddConstantValue("Price Source Group"::Vendor, PriceListLine.FieldNo("Source Group"));
    PriceListLineDataTransfer.CopyFields();
    Clear(PriceListLineDataTransfer);

    // Update Customer-related records
    PriceListLineDataTransfer.SetTables(Database::"Price List Line", Database::"Price List Line");
    PriceListLineDataTransfer.AddSourceFilter(PriceListLine.FieldNo("Source Group"), '=%1', "Price Source Group"::All);
    PriceListLineDataTransfer.AddSourceFilter(PriceListLine.FieldNo("Source Type"), '<>%1&<>%2&<>%3', 
        "Price Source Type"::"All Jobs", "Price Source Type"::Job, "Price Source Type"::"Job Task");
    PriceListLineDataTransfer.AddSourceFilter(PriceListLine.FieldNo("Price Type"), '=%1', "Price Type"::Sale);
    PriceListLineDataTransfer.AddConstantValue("Price Source Group"::Customer, PriceListLine.FieldNo("Source Group"));
    PriceListLineDataTransfer.CopyFields();

    UpgradeTag.SetUpgradeTag(UpgradeTagDefinitions.GetPriceSourceGroupFixedUpgradeTag());
end;
```

**BAD Example (Loop/Modify - Avoid for Large Data):**
```al
 ItemJournalLine.SetLoadFields("Cross-Reference No.", "Item Reference No.");
    ItemJournalLine.SetFilter("Cross-Reference No.", '<>%1', '');
    if ItemJournalLine.FindSet() then
        repeat
            ItemJournalLine."Item Reference No." := ItemJournalLine."Cross-Reference No.";
            ItemJournalLine.Modify();
        until ItemJournalLine.Next() = 0;
```

**GOOD Example (DataTransfer - Use for Large Data):**
```al
    ItemJournalLine.SetFilter("Item Reference No.", '<>%1', '');
    if ItemJournalLine.IsEmpty() then begin
        ItemJournalLineDataTransfer.SetTables(Database::"Item Journal Line", Database::"Item Journal Line");
        ItemJournalLineDataTransfer.AddSourceFilter(ItemJournalLine.FieldNo("Cross-Reference No."), '<>%1', '');
        ItemJournalLineDataTransfer.AddFieldValue(ItemJournalLine.FieldNo("Cross-Reference No."), ItemJournalLine.FieldNo("Item Reference No."));
        ItemJournalLineDataTransfer.CopyFields();
    end;
```

## 9. InitValue and Upgrade Code Connection

### Rule: New Fields with InitValue Need Upgrade Code
When a field is added with InitValue:
- InitValue applies ONLY to new records
- Existing records get datatype default (0 for numbers, false for Boolean)
- Code reviewer MUST ask if upgrade code is needed for each of the fields. 

**Example Field Addition:**
```al
field(100; "New Field"; Boolean)
{
    DataClassification = CustomerContent;
    Caption = 'New Field';
    InitValue = true;
}

field(101; "New Field 2"; Integer)
{
    DataClassification = CustomerContent;
    Caption = 'New Field 2';
    InitValue = 5;
}
```

**Required Upgrade Code:**
```al
local procedure UpgradeMyTables()
var
    BlankMyTable: Record "My Table";
    UpgradeTag: Codeunit "Upgrade Tag";
    UpgradeTagDefinitions: Codeunit "Upgrade Tag Definitions";
    MyTableDataTransfer: DataTransfer;
begin
    if UpgradeTag.HasUpgradeTag(UpgradeTagDefinitions.GetUpgradeMyTablesTag()) then
        exit;

    MyTableDataTransfer.SetTables(Database::"My Table", Database::"My Table");
    MyTableDataTransfer.AddConstantValue(true, BlankMyTable.FieldNo("New Field"));
    MyTableDataTransfer.AddConstantValue(5, BlankMyTable.FieldNo("New Field 2"));    
    MyTableDataTransfer.CopyFields();

    UpgradeTag.SetUpgradeTag(UpgradeTagDefinitions.GetUpgradeMyTablesTag());
end;
```

## Review Checklist

When reviewing upgrade code, verify:

1. ✅ No direct code in OnUpgrade triggers (only method calls)
2. ✅ No OnValidate or OnCheckPreconditions triggers without justification
3. ✅ All database read operations are protected with IF-THEN
4. ✅ Upgrade tags used instead of version checks
5. ✅ No external calls (HTTP, DotNet interop)
6. ✅ DataTransfer used for tables > 300k records
7. ✅ DataTransfer only used for new fields/tables
8. ✅ InitValue fields have corresponding upgrade code. Each new file **MUST** be verified.
9. ✅ Proper error handling (minimal blocking)
10. ✅ Upgrade tags properly registered with event subscribers

## Common Anti-Patterns to Flag

- Version checking instead of upgrade tags
- Direct database operations without IF protection
- Loop/Modify pattern on large datasets
- Missing upgrade code for InitValue fields
- External service calls during upgrade
- Complex nested upgrade tag logic
- Direct implementation in OnUpgrade triggers