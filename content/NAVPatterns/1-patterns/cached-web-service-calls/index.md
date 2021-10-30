+++
title = "Cached Web Server Calls"
weight = 290
+++
_Originally by Mostafa Balat, Microsoft Development Center Copenhagen_

## Abstract

In a service-oriented deployment, web services are used to extend NAV's functionality and reach. Depending on how volatile this data is and the corresponding usage scheme, it is expected to be up-to-date within a pre-defined period of time (e.g. once a day).

## Description

When NAV is integrated with external services, then the user scenarios become dependent on the data and functions offered by such services. Eventually, there are different approaches through which the external data can be retrieved, stored and used.

* Dynamic: either exposed by the external service itself or by a separate catalog that NAV can query.
  * Advantage: data is always up-to-date
  * Disadvantage: it requires constant connection to the data source.
* Static: hard-coded in the database for the user to benefit from.
  * Advantage: data is promptly available when needed.
  * Disadvantage: if data changes at some point, it will require a maintenance effort, which exposes the business process to a risk of failure.
* Cached: offered through an external service and gets pulled according to a pre-defined refresh rate or manually.
  * Advantage: data is 'up-to-date' within the rules acceptable by the business process, without extra load on the network resources or the external service.
  * Disadvantage: if data changes while the auto-refresh did not happen yet, the user may not have access to the latest data; however, the user can manually force a refresh of the data, if asked to do so.

### When to Use It

Offer data in lookups that were cached from an external service.

### Diagram

[![ ][image0]][anchor0]

## Usage

Set up an NAV feature to consume the data from an external service. Refresh the data on a pre-defined refresh rate (e.g. once a day) or when enforced by a power user or an admin. Cache the data in a table and offer it in lookups, as applicable.

## NAV Specific Example

### Overview

**PAG1259 Bank Name - Data Conv. List** offers the required functionality to refresh and display the list of bank names needed to specify which file format to convert to. The page can be accessed from **PAG1260 Bank Data Conv. Service Setup** to display all available bank names. It is also used as a lookup on **PAG370 Bank Account Card**, where it offers a filtered view of the cached bank names based on the Country/Region Code field.

If **PAG1259 Bank Name - Data Conv. List** is being open and the cached data is 'old', it refreshes the cache. The cached data is stored in **TAB1259 Bank Data Conv. Bank**. Meanwhile, the user has the chance to refresh the data using

### Code Sample

```AL
OnInit=BEGIN
    ShortTimeout := 5000;
    LongTimeout := 30000;
END;

OnOpenPage=VAR
    BankDataConvBank@1002 : Record 1259;
    ImpBankListExtDataHndl@1000 : Codeunit 1289;
    CountryRegionCode@1004 : Text;
    HideErrors@1003 : Boolean;
BEGIN
    CountryRegionCode := IdentifyCountryRegionCode(Rec,GETFILTER("Country/Region Code"));
    IF BankDataConvBank.ISEMPTY THEN BEGIN
        ImpBankListExtDataHndl.GetBankListFromConversionService(HideErrors,CountryRegionCode,ShortTimeout);
        EXIT;
    END;
    RefreshBankNamesOlderThanToday(CountryRegionCode,HideErrors,ShortTimeout);
END;

OnAction=VAR
    ImpBankListExtDataHndl@1000 : Codeunit 1289;
    FilterNotUsed@1001 : Text;
    ShowErrors@1003 : Boolean;
BEGIN
    ShowErrors := TRUE;
    ImpBankListExtDataHndl.GetBankListFromConversionService(ShowErrors,FilterNotUsed,LongTimeout);
END;

LOCAL PROCEDURE IdentifyCountryRegionCode@1(VAR BankDataConvBank@1002 : Record 1259;Filter@1000 : Text) : Text;
    VAR
        CompanyInformation@1001 : Record 79;
        BlankFilter@1003 : Text;
    BEGIN
        BlankFilter := '''''';
        IF Filter = BlankFilter THEN BEGIN
            CompanyInformation.GET;
            BankDataConvBank.SETFILTER("Country/Region Code",CompanyInformation."Country/Region Code");
            EXIT(BankDataConvBank.GETFILTER("Country/Region Code"));
        END;
        EXIT(Filter);
    END;

LOCAL PROCEDURE RefreshBankNamesOlderThanToday@5(CountryRegionCode@1000 : Text;ShowErrors@1002 : Boolean;Timeout@1004 : Integer);
    VAR
        BankDataConvBank@1001 : Record 1259;
        ImpBankListExtDataHndl@1003 : Codeunit 1289;
    BEGIN
        IF CountryRegionCode <\> '' THEN
            BankDataConvBank.SETFILTER("Country/Region Code",CountryRegionCode);
    BankDataConvBank.SETFILTER("Last Update Date",'<%1',TODAY);
    IF BankDataConvBank.FINDFIRST THEN
        ImpBankListExtDataHndl.GetBankListFromConversionService(ShowErrors,CountryRegionCode,Timeout);
END;
```
## NAV Usages

Bank name lookup on the Bank Account card for dynamically identifying the format to use to generate a bank-specific payment file.

## Ideas for Improvement

Expose the refresh rate through a setup table to make it easily configurable without changing the code.



[anchor0]: Cached_5F00_Web_5F00_Service_5F00_Calls_5F00_Diagram.png


[image0]: Cached_5F00_Web_5F00_Service_5F00_Calls_5F00_Diagram.png
