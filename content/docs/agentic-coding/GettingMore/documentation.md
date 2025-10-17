---
title: "Generating Documentation"
linkTitle: "Documentation"
weight: 2
description: >
  Use AI to create and maintain comprehensive documentation for your AL code
---

## Scenario

You have a well-functioning AL extension but it lacks documentation. You need to create:
- XML documentation comments for all procedures
- A comprehensive README file
- API documentation for public interfaces
- User-facing feature documentation

## Goal

Use AI to quickly generate high-quality documentation that:
- Explains what the code does
- Documents parameters and return values
- Provides usage examples
- Helps developers and users understand the extension

## Types of Documentation

### 1. XML Documentation Comments
In-code documentation for procedures, triggers, and events.

### 2. README Files
Project overview, setup instructions, and getting started guides.

### 3. API Documentation
Documentation for public APIs and integration points.

### 4. User Documentation
End-user guides and feature explanations.

## Step-by-Step Guide

### Step 1: XML Documentation for Procedures

**Scenario**: You have a codeunit without documentation.

**Starting Code**:
```al
codeunit 50100 "Customer Email Manager"
{
    procedure SendWelcomeEmail(CustomerNo: Code[20]): Boolean
    var
        Customer: Record Customer;
        EmailMessage: Codeunit "Email Message";
    begin
        if not Customer.Get(CustomerNo) then
            exit(false);

        if Customer."E-Mail" = '' then
            exit(false);

        EmailMessage.Create(Customer."E-Mail", 'Welcome!', GetWelcomeEmailBody(Customer));
        exit(EmailMessage.Send());
    end;

    local procedure GetWelcomeEmailBody(Customer: Record Customer): Text
    begin
        exit(StrSubstNo('Dear %1, welcome to our service!', Customer.Name));
    end;
}
```

**Prompt**:
```
Add XML documentation comments to all procedures in this codeunit.
Include:
- Summary for each procedure
- Parameter descriptions
- Return value descriptions
- Example usage where helpful
- Any important notes or warnings

[Paste the code]
```

**Expected Result**:
```al
/// <summary>
/// Manages customer email communications
/// </summary>
codeunit 50100 "Customer Email Manager"
{
    /// <summary>
    /// Sends a welcome email to a customer
    /// </summary>
    /// <param name="CustomerNo">The customer number to send the email to</param>
    /// <returns>True if the email was sent successfully, false otherwise</returns>
    /// <remarks>
    /// The procedure will return false if:
    /// - The customer does not exist
    /// - The customer has no email address
    /// - The email fails to send
    /// </remarks>
    /// <example>
    /// if CustomerEmailManager.SendWelcomeEmail('C001') then
    ///     Message('Welcome email sent successfully');
    /// </example>
    procedure SendWelcomeEmail(CustomerNo: Code[20]): Boolean
    var
        Customer: Record Customer;
        EmailMessage: Codeunit "Email Message";
    begin
        if not Customer.Get(CustomerNo) then
            exit(false);

        if Customer."E-Mail" = '' then
            exit(false);

        EmailMessage.Create(Customer."E-Mail", 'Welcome!', GetWelcomeEmailBody(Customer));
        exit(EmailMessage.Send());
    end;

    /// <summary>
    /// Generates the body text for the welcome email
    /// </summary>
    /// <param name="Customer">The customer record to generate the email for</param>
    /// <returns>The formatted email body text</returns>
    local procedure GetWelcomeEmailBody(Customer: Record Customer): Text
    begin
        exit(StrSubstNo('Dear %1, welcome to our service!', Customer.Name));
    end;
}
```

### Step 2: Batch Documentation for Multiple Files

**Prompt**:
```
I have multiple AL files that need XML documentation.
For each public procedure, add:
- Summary
- Parameter descriptions  
- Return value (if applicable)
- Example usage

Process these files:
[List files or paste multiple code blocks]
```

**Tip**: Do this in batches of 3-5 files to maintain context and quality.

### Step 3: Creating a README File

**Prompt**:
```
Create a comprehensive README.md for my AL extension with the following information:

Extension Name: Customer Engagement Suite
Purpose: Enhance customer communication and engagement features
Key Features:
- Automated welcome emails
- Customer portal access
- Newsletter subscription management
- Customer satisfaction surveys

Include sections for:
- Overview and purpose
- Features list
- Installation instructions
- Configuration steps
- Usage examples
- Dependencies
- Compatibility (BC version 21+)
- Contributing guidelines
- License (MIT)
```

**Expected Structure**:
```markdown
# Customer Engagement Suite

> Enhance customer communication and engagement in Microsoft Dynamics 365 Business Central

## Overview

The Customer Engagement Suite is a Business Central extension that provides comprehensive 
tools for managing customer communications and engagement activities.

## Features

### Automated Welcome Emails
Automatically send personalized welcome emails to new customers upon registration.

### Customer Portal Access
Enable customers to access a self-service portal for order tracking and account management.

### Newsletter Subscription Management
Allow customers to manage their newsletter preferences and subscriptions.

### Customer Satisfaction Surveys
Collect and analyze customer feedback through integrated surveys.

## Installation

### Prerequisites
- Microsoft Dynamics 365 Business Central version 21.0 or higher
- Email account configured in Business Central
- System Application extension installed

### Steps

1. Download the extension file (.app)
2. Open Business Central Administration Shell
3. Run the installation command:
   ```powershell
   Publish-NAVApp -ServerInstance BC210 -Path "CustomerEngagementSuite.app"
   Install-NAVApp -ServerInstance BC210 -Name "Customer Engagement Suite"
   ```

## Configuration

### Email Setup
1. Navigate to **Email Accounts** page
2. Configure your SMTP settings
3. Test the email connection

### Welcome Email Template
1. Go to **Customer Engagement Setup**
2. Configure the welcome email template
3. Enable automatic sending

## Usage

### Sending Welcome Emails
```al
codeunit 50100 "Customer Email Manager"
{
    procedure SendWelcomeEmail(CustomerNo: Code[20]): Boolean
}
```

**Example:**
```al
CustomerEmailManager: Codeunit "Customer Email Manager";
begin
    if CustomerEmailManager.SendWelcomeEmail('C001') then
        Message('Welcome email sent successfully');
end;
```

## Dependencies

- **System Application**: Core BC functionality
- **Base Application**: Standard BC tables and pages

## Compatibility

- **Business Central**: Version 21.0 and higher
- **Platform**: Compatible with both SaaS and On-Premises

## Contributing

We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## License

This project is licensed under the MIT License - see [LICENSE](LICENSE) file for details.

## Support

For issues and questions:
- Create an issue in our [GitHub repository](https://github.com/yourorg/customer-engagement)
- Email: support@yourcompany.com

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for version history.
```

### Step 4: API Documentation

**Prompt**:
```
Create API documentation for the public procedures in my Customer Email Manager codeunit.
Format it as markdown suitable for a developer portal.

Include:
- Procedure signature
- Description
- Parameters with types and descriptions
- Return values
- Code examples
- Error conditions
- Notes and best practices

[Include the codeunit code]
```

**Expected Output**:
```markdown
# Customer Email Manager API

## Overview
The Customer Email Manager provides functionality for sending customer-related emails.

## Procedures

### SendWelcomeEmail

Sends a welcome email to a specified customer.

**Signature:**
```al
procedure SendWelcomeEmail(CustomerNo: Code[20]): Boolean
```

**Parameters:**

| Name | Type | Description |
|------|------|-------------|
| CustomerNo | Code[20] | The unique identifier of the customer |

**Returns:**

| Type | Description |
|------|-------------|
| Boolean | `true` if email sent successfully, `false` otherwise |

**Example:**
```al
var
    CustomerEmailManager: Codeunit "Customer Email Manager";
    Success: Boolean;
begin
    Success := CustomerEmailManager.SendWelcomeEmail('C00001');
    if Success then
        Message('Email sent successfully')
    else
        Error('Failed to send email');
end;
```

**Error Conditions:**

The procedure returns `false` in the following cases:
- Customer does not exist
- Customer has no email address configured
- Email service is unavailable

**Best Practices:**
- Always check the return value
- Ensure email account is configured before calling
- Consider calling asynchronously for better performance

**See Also:**
- Email Account Setup
- Customer Configuration
```

### Step 5: User Documentation

**Prompt**:
```
Create user-facing documentation for the Welcome Email feature.
Target audience: Business users who are not developers.

Explain:
- What the feature does
- How to enable/configure it
- How to use it
- Troubleshooting common issues

Use simple language and include screenshots placeholders where helpful.
```

**Expected Output**:
```markdown
# Welcome Email Feature

## What is the Welcome Email Feature?

The Welcome Email feature automatically sends a personalized email to new customers 
when they are added to your system. This helps create a positive first impression 
and provides important information to your customers right away.

## Enabling Welcome Emails

1. Open Business Central
2. Search for "Customer Engagement Setup"
3. Check the box next to "Send Welcome Emails"
4. Click "OK" to save

![Setup Screenshot]

## Configuring the Email Template

### Step 1: Access Email Templates
1. Search for "Email Templates"
2. Find "Customer Welcome Email"
3. Click "Edit"

### Step 2: Customize the Message
1. Update the email subject line
2. Modify the email body
3. Add your company logo
4. Click "OK" to save

### Step 3: Test the Email
1. Open a test customer
2. Click "Actions" > "Send Welcome Email"
3. Check that you receive the email

## Using Welcome Emails

### Automatic Sending
When enabled, welcome emails are sent automatically when:
- A new customer is created
- An existing customer is marked as active for the first time

### Manual Sending
You can also send welcome emails manually:

1. Open the customer card
2. Click "Actions" > "Email" > "Send Welcome Email"
3. Confirm the action

## Troubleshooting

### Email Not Sending

**Problem:** Welcome emails are not being sent

**Solutions:**
- Check that the feature is enabled in setup
- Verify the customer has an email address
- Confirm your email account is configured correctly
- Check that email service is running

### Wrong Email Template

**Problem:** Customers receive the wrong email content

**Solutions:**
- Review the email template configuration
- Ensure you're editing the correct template
- Clear any cached templates

### Customer Not Receiving Email

**Problem:** Email shows as sent but customer didn't receive it

**Solutions:**
- Ask customer to check spam folder
- Verify the email address is correct
- Check email delivery logs
- Test with a different email address

## Tips and Best Practices

- **Personalization**: Use customer name and company name in the email
- **Timing**: Send welcome emails within 24 hours of customer creation
- **Follow-up**: Consider a follow-up email series
- **Feedback**: Ask customers if they received and found the email helpful

## Need Help?

Contact your Business Central administrator or support team for assistance.
```

## Review Points

After generating documentation, verify:

### ✅ Accuracy
- [ ] Documentation matches actual code behavior
- [ ] Parameter descriptions are correct
- [ ] Return values accurately described
- [ ] Examples work as shown

### ✅ Completeness
- [ ] All public procedures documented
- [ ] All parameters explained
- [ ] Edge cases mentioned
- [ ] Error conditions documented

### ✅ Clarity
- [ ] Easy to understand
- [ ] Appropriate level of detail
- [ ] Good examples provided
- [ ] Proper formatting

### ✅ Maintenance
- [ ] Easy to update
- [ ] Versioned appropriately
- [ ] Consistent style
- [ ] Well organized

## Advanced Documentation Prompts

### Generate CHANGELOG
```
Create a CHANGELOG.md file documenting these changes from version 1.0 to 2.0:
- Added: Customer portal access
- Added: Newsletter preferences
- Changed: Welcome email now includes portal link
- Fixed: Email encoding issues with special characters
- Deprecated: Old email API (will be removed in 3.0)

Follow Keep a Changelog format.
```

### Create Migration Guide
```
Create a migration guide for users upgrading from version 1.x to 2.0.
Include:
- Breaking changes
- New features
- Configuration changes needed
- Data migration steps
- Deprecation warnings
```

### Generate Inline Code Comments
```
Add helpful inline comments to this complex procedure explaining the logic flow.
Don't over-comment obvious code, but do explain:
- Complex algorithms
- Business rule implementations
- Non-obvious optimizations
- Workarounds

[Paste code]
```

## Keeping Documentation Updated

### When Code Changes
**Prompt**:
```
I've updated this procedure to add a new parameter.
Update the XML documentation to reflect the change:

Old procedure:
[paste old code]

New procedure:
[paste new code]
```

### Regular Documentation Reviews
**Prompt**:
```
Review the documentation for this codeunit.
Check for:
- Outdated information
- Missing documentation
- Incorrect examples
- Deprecated features

[Paste codeunit]
```

## Best Practices

### 1. Document As You Code
```
I'm about to write a procedure to validate customer credit limits.
Create the XML documentation comment first, then we'll implement the procedure.
```

### 2. Use Consistent Style
Create a documentation template:
```
Create an XML documentation template I can use for all my procedures.
Include sections for: summary, parameters, returns, exceptions, examples, and remarks.
```

### 3. Generate Documentation in Batches
Document related code together for consistency:
```
Document all procedures in this codeunit that relate to email sending.
Use consistent terminology and structure.
```

### 4. Include Real Examples
```
Add a realistic code example to this procedure's documentation showing:
- Typical usage
- Error handling
- Integration with other features
```

## Common Documentation Patterns

### For Validation Procedures
```
Document this validation procedure. Include:
- What is being validated
- Valid conditions
- Error messages that can be raised
- Example of valid and invalid inputs
```

### For Event Subscribers
```
Document this event subscriber. Include:
- What event it subscribes to
- When it triggers
- What it does
- Side effects or implications
- Integration points
```

### For APIs
```
Create REST API documentation for this AL API page.
Include:
- Endpoint URL
- HTTP methods supported
- Request/response examples
- Authentication requirements
- Error codes
```

## Practice Exercise

Generate documentation for this code:

```al
codeunit 50110 "Order Status Manager"
{
    procedure UpdateOrderStatus(OrderNo: Code[20]; NewStatus: Enum "Order Status"): Boolean
    var
        SalesHeader: Record "Sales Header";
    begin
        if not SalesHeader.Get(SalesHeader."Document Type"::Order, OrderNo) then
            exit(false);

        SalesHeader.Status := NewStatus;
        SalesHeader.Modify(true);
        SendStatusNotification(OrderNo, NewStatus);
        exit(true);
    end;

    local procedure SendStatusNotification(OrderNo: Code[20]; Status: Enum "Order Status")
    begin
        // Implementation
    end;
}
```

**Your Tasks**:
1. Generate XML documentation
2. Create a README section explaining this feature
3. Write API documentation
4. Create user documentation
5. Review and improve the generated docs

## Next Steps

- Learn how to use AI for [adding telemetry](../telemetry)
- Explore [refactoring legacy code](../refactoring) while maintaining documentation
- See how to conduct [AI-assisted code reviews](../code-review)
