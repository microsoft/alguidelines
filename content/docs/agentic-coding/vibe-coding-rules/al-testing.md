---
title: "AL Testing & Project Structure Rules"
description: >
  AL-Go workspace structure, test generation guidelines, and project organization rules
globs: ["*.al", "app.json"]
alwaysApply: false
---

# AL Testing & Project Structure Rules

These rules ensure proper project organization, test implementation, and workspace structure in AL-Go based development environments.

## Rule 1: AL-Go Workspace Structure Guidelines

### Intent
Establish clear separation between application code and test code in AL-Go workspace environments. 

- App project contains Tables, Pages, Codeunits, Reports, APIs, Enums, etc. 
- Test project contains Test Codeunits, Test Pages, Mock objects, Test data. 
- Each project has its own app.json with appropriate dependencies. 
- Test project references the App project as a dependency. 
- Use App project ONLY for main application implementation, use Test project ONLY for test implementation, never include test files in the main App folder, and never include application logic in the Test folder. 
- When working in AL-Go workspace, always place files in the correct project based on their purpose.

### Examples

```
// Good example - Proper AL-Go workspace structure
Repository/
├── .AL-Go/
├── .github/
├── App/
│   ├── src/
│   │   ├── Setup/
│   │   ├── Feature1/
│   │   ├── Feature2/
│   │   ├── APIs/
│   ├── app.json
│   └── launch.json
├── Test/
│   ├── src/
│   │   ├── SetupTests/
│   │   ├── Feature1Tests/
│   │   ├── Feature2Tests/
│   │   ├── IntegrationTests/
│   ├── app.json
│   └── launch.json
└── al.code-workspace
```

## Rule 2: Test Generation Guidelines

### Intent
Control when and how test code is generated to maintain focus on main application implementation. 

- DO NOT automatically generate test code unless explicitly requested
- Focus on main application implementation by default
- When user asks for implementation create only the main application objects
- Only generate test files when user specifically requests "Create tests for...", "Generate unit tests...", "Add test coverage...", or "Write tests..."
- If tests are requested, place them in the Test project following the folder structure where test files should mirror the App project structure but in the Test project
- Unless the user explicitly requests tests, focus only on main application implementation

## Rule 3: Project Dependencies Configuration

### Intent
Establish correct dependency relationships between App and Test projects. 

- App project app.json should NOT reference Test project
- Test project app.json MUST reference App project as dependency
- Test project should include testing frameworks (e.g., "Any", "Library Assert"), and each project maintains its own dependencies. 
- When configuring project dependencies, ensure Test project references App project but never the reverse and include appropriate testing frameworks in Test project.

### Examples

```json
// Good example - Test project app.json
{
  "dependencies": [
    {
      "id": "your-app-id",
      "name": "Your App Name", 
      "publisher": "Your Publisher",
      "version": "1.0.0.0"
    },
    {
      "id": "dd0be2ea-f733-4d65-bb34-a28f4624fb14",
      "name": "Library Assert",
      "publisher": "Microsoft",
      "version": "20.0.0.0"
    }
  ]
}
```

## Rule 4: Unit Testing Best Practices

### Intent
Write comprehensive unit tests that ensure reliability of business logic. 

- Write unit tests for all business logic
- Follow given/when/then structure for test naming
- Use Assert statements for validating critical conditions
- Create test data factories for consistent test setup
- Always try to use standard library codeunits to create data and post documents
- When creating tests, use descriptive names that follow given/when/then pattern and include comprehensive assertions to validate expected behavior.

### Examples

```al
// Good example - Well-structured unit test with standard library codeunits
codeunit 50200 "Customer Management Tests"
{
    Subtype = Test;
    
    var
        Assert: Codeunit Assert;
        LibrarySales: Codeunit "Library - Sales";
        LibraryInventory: Codeunit "Library - Inventory";
        LibraryRandom: Codeunit "Library - Random";
        LibraryERM: Codeunit "Library - ERM";
    
    [Test]
    procedure GivenValidCustomer_WhenCreatingCustomer_ThenCustomerIsCreated()
    var
        Customer: Record Customer;
        CustomerManagement: Codeunit "Customer Management";
        CustomerNo: Code[20];
    begin
        // Given - Valid customer data using library
        LibrarySales.CreateCustomer(Customer);
        Customer."Credit Limit (LCY)" := LibraryRandom.RandDec(10000, 2);
        
        // When - Creating customer
        CustomerNo := CustomerManagement.CreateCustomer(Customer);
        
        // Then - Customer is created successfully
        Assert.IsTrue(Customer.Get(CustomerNo), 'Customer should be created');
        Assert.AreEqual(Customer.Name, Customer.Name, 'Customer name should match');
    end;
    
    [Test]
    procedure GivenSalesOrder_WhenPostingOrder_ThenInvoiceIsCreated()
    var
        SalesHeader: Record "Sales Header";
        SalesLine: Record "Sales Line";
        Item: Record Item;
        PostedInvoiceNo: Code[20];
    begin
        // Given - Sales order with library-created data
        LibraryInventory.CreateItem(Item);
        LibrarySales.CreateSalesHeader(SalesHeader, SalesHeader."Document Type"::Order, '');
        LibrarySales.CreateSalesLine(SalesLine, SalesHeader, SalesLine.Type::Item, Item."No.", LibraryRandom.RandInt(10));
        
        // When - Posting sales order
        PostedInvoiceNo := LibrarySales.PostSalesDocument(SalesHeader, true, true);
        
        // Then - Posted invoice exists
        Assert.AreNotEqual('', PostedInvoiceNo, 'Posted invoice should be created');
    end;
}
```

## Rule 5: Feature-Based Test Organization

### Intent
Organize test files to mirror the application structure while maintaining clear separation. 

- Test files should mirror App project structure in Test project
- Use same feature-based organization for tests
- Place shared test utilities in Common folder
- Maintain consistent naming patterns

### Examples

```
// Good example - Mirrored test structure
App/src/
├── NoSeries/
│   ├── NoSeries.Table.al
│   └── NoSeries.Page.al
└── Sales/
    └── Invoice/
        └── SalesInvoice.Page.al

Test/src/
├── NoSeries/
│   └── NoSeriesTests.Codeunit.al
├── Sales/
│   └── Invoice/
│       └── SalesInvoiceTests.Codeunit.al
└── Common/
    └── TestHelpers/
        └── TestDataFactory.Codeunit.al
``` 