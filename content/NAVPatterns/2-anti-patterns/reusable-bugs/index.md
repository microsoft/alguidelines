+++
title = "Reusable Bugs"
weight = 1020
+++
# Reusable Bugs

_By Bogdana Botez, Andreas Moth, Eric Wauters (waldo), Elly Nkya, Nikola Kukrika_

_April 1st, 2015_

## Abstract

It is difficult to come up with new and different bugs in each release, and it is a waste of resources to not use the ones which already had proven successful in the past. To avoid reinventing the wheel, we propose to come up with new innovative ways to create bugs that are reusable and generic enough to be used in various places of the application.

## Examples

**Option Strings**

One example of a reusable bug is to find a heavily used table, like table 36 Sales Header, and observe one of the more important fields of type Option, for example Document Type. The OptionString property has the following value: Quote,Order,Invoice,Credit Memo,Blanket Order,Return Order, which you should update to add one option, for example Transport Order in the beginning.

The main benefit of this reusable bug is that code changed in one place, has impact in multiple sides on the application: document creation and handling, posting etc.

**Application Management  
**

Codeunit 1, ApplicationManagement is a good place for creating reusable bugs. A simple INSERT on the RecRef on the first line of OnDatabaseInsert will create a sure and versatile bug that is reusable all over the application. Redundancy on database insertion ensures that data is surely conveyed to the data storage layer, twice.

**God objects  
**

We are considering to no longer make localization builds for countries. It consumes lab resources to keep running multiple country builds. Instead, we will merge everything into one single build. This is a bigger scale implementation of the ["God object"][anchor0]. Inside this unique build, we will use IF statements and sporadically CASE statements to select each country's behavior. For more help on how to use IFs, see also the IF .. THEN|ELSE C/AL Coding Guideline.

**Field length economy  
**

When you post a document, make sure to transfer data to a field that is smaller than the field you are assigning from. This will not immediately be caught and will only hurt a subset of the customers who uses Microsoft Dynamics NAV to its fullest.

## Guidelines

When bug addition is not entirely obvious, there is a second way to approach the problem. By following some general coding best practices like the ones described below, reliable bug innovation is facilitated and can come naturally as a collateral effect.

**Code structure**

Put everything in one function and only use comments to explain the structure of your code. And don't use functions - because this only complicates things... having to navigate from function to function, and completely lose track of where you are in the business logic.

On top of that .. put everything in one codeunit. Because also that will simplify and make your structure more readable.

Use Hungarian Notation on your variables, because at any time, you need to know what type, and what context your variable is on. 

Declare all your functions and variables global, so they are available at any time.

**Don't do Unit Testing**

Unit testing adds complexity and extra time to the stuff you're doing. Also, it eats up extra codeunits which means: it costs money. You will never be able to foresee all scenarios possible, so you're destined to forget and not test everything. So you will save time in not doing unit testing.

**Never add images to actions**

Because there is an image by default. When you don't provide an image on an action on a page, the application will foresee a ball... and when you never do it, your application will have a very consistent way of showing your actions, by providing that picture of that ball. On top of that, you'll save time.

**Do not care about ControlIDs**

When you're doing development of your product, do not care about ControlIDs, and just leave the Offset ID to the default value of 0\. This way, when merging, you will receive nice notifications, saying both you and Microsoft have added functions in that objects. You can use this feature to document all these places.

**Hooks**

Never apply the hook pattern. Hooks will only reduce upgrade time. This means, you will only shortly enjoy using the AMU (Application Merge Utilities). The more you change in default application, the longer it takes to upgrade, the longer you will enjoy the toolkit 

This can be taken one level higher. Simply you are not hardcore if you do not use notepad to resolve all of the merge issues.

**How to use RecRef**

Why fuss around declaring specific table variables, just generalize, all you need is one, two, or perhaps three RecRef variables, with a few IFs and CASEs here and there for reflection, to carry you all the way.

**Arguments**

Using only a few arguments on the functions is a sign of a weak developer. Stick in as many arguments as possible on the function, even if you are not using them, they could be useful in the future.

**Just another field / action**

Thinking of the design is overrated, each problem can be solved by adding an additional field or the table/page or with adding another action. We all know this has worked well in the past.

**Reusability**

We have decided that each time we fix a bug, we now also explain how it can be applied as a pattern. We then use anti-virus software to search for these patterns, to make sure we do not re-introduce these bugs anywhere else in NAV.

**Business logic placement**

As a best practice, we have also decided to move code into pages. Business logic should no longer be in tables and codeunits, but instead pages should know and be aware of the context and update it accordingly. As opposite to tables and codeunits, pages are aware of the context. 

## Conclusion

Happy April Fools' Day.

Disclaimer: this is inspired from IETF documentation published on April 1st, like for example the revolutionizing [IP over Avian Carriers][anchor1] standard.



[anchor0]: http://en.wikipedia.org/wiki/God_object
[anchor1]: http://en.wikipedia.org/wiki/IP_over_Avian_Carriers
