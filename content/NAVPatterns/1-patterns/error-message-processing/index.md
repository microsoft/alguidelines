+++
title = "Error Message Processing"
weight = 550
+++
_By Jesper Schulz at Microsoft Development Center Copenhagen_

Note: This pattern describes new functionality which makes it possible to generalize the [Journal Error Processing][anchor0] pattern.

## Abstract

Missing, invalid or incomplete data is a common issue during data processing in NAV. This article describes how to collect all error messages during processing using the error message component and present them to the user in a unified way, which enables the user to correct the errors efficiently. By leveraging the integrated error message logging functions, you can log a message with a single line of code and present it to the user with another one-liner.

## Description

This article describes how to use the Error Message component in NAV, which in short gives you the possibility to:

1. Link an error message to the page which enables you to resolve the problem.  
2. Assemble all error messages in one central view instead of having to encounter them one by one.

Validating data is a common task during data processing in NAV. Unfortunately, validation is often done using NAV's integrated ERROR and TESTFIELD functions, which halt execution of the process. The user will then have to locate the invalid / missing data, correct it and reinitiate the process, possibly running into the next error, making the cycle repeat itself. This can be a very tedious, time-consuming and frustrating process. The error message component aims at improving this experience by providing a lightweight framework for error message logging and this article will explain how to leverage this functionality in your code. By doing so, all error messages are gathered during (pre-)processing and are finally presented to the user. The user then has the possibility to click on the error message, which will open the record where the invalid / missing data is located, thereby enabling the user to correct all mistakes efficiently, from one central place and in one go.

The example below comes from a Mexican localization, where the user has to export financial balances and transactions into an XML file for government audit purposes. In order to generate valid files, some mandatory data needs to be entered in the system. By leveraging the error message component, the user will be presented with the following page, if missing or invalid data was discovered:

[![ ][image0]][anchor1]

By clicking on the error message, the user will be presented with the entity, where the invalid / missing data should be corrected / added. This is done by applying the related pattern [Easy Update of Setup or Supplementary Information][anchor2].

[![ ][image1]][anchor3]

## Usage

In the processing function, define a temporary record of type "Error Message". Use the functions on that record to populate the record with error message, a few of them being:

* **LogIfEmpty**
* **LogIfLengthExceeded**
* **LogIfInvalidCharacters**
* **LogIfOutsideRange**
* **LogIfGreaterThan**
* **LogIfEqualTo**
* **LogMessage**

The following parameters must be provided to these functions:

* **Record:** The record which you want to check
* **Field Number:** The field number of the field you want to verify the value of
* **Condition:** The condition the field must meet (e.g. length, range, valid characters)
* **Message Type:** The type of message, which can be Error, Warning or Message

When the processing is complete, you can check if any error messages of type "Error" were logged by calling the **HasErrors** function and you can show the list or error messages by calling the **ShowErrorMessages** function. You can also integrate the error messages list as a FactBox, but that is not part of this example.

## NAV Specific Example

The code below is an example of how the error message component was used in one part of the before mentioned Mexican feature. This code iterates over all G/L Accounts and pipes information out into an XML file. While doing so, it is validated that all mandatory fields have values and meet certain conditions. And only if that is the case, is the XML document actually exported. Also notice, that an error message is logged, in case no G/L Accounts are found given the provided filters. That way, the user can be guided to setup the system correctly.

```AL
    PROCEDURE ExportChartOfAccounts@1(Year@1000 : Integer;Month@1001 : Integer);
    VAR
        TempErrorMessage@1003 : TEMPORARY Record 700;
    BEGIN
    TempErrorMessage.ClearLog; // only necessary if variable is global
    ...
    CreateXMLHeader(Document,RootNode,CatalogoNodeTxt,Namespace,Year,Month,'1.1');
    IF GLAccount.FINDSET THEN BEGIN
        REPEAT
            TempErrorMessage.LogIfEmpty (GLAccount,GLAccount.FIELDNO(Name),TempErrorMessage."Message Type"::Error);
        
            XMLDOMManagement.AddElement(RootNode,'Ctas','',Namespace,Node);
            XMLDOMManagement.AddAttribute(Node,'CodAgrup',GLAccount."SAT Account Code");
            ...
            CASE GLAccount."Debit/Credit" OF
                GLAccount."Debit/Credit"::Debit:
                    XMLDOMManagement.AddAttribute(Node,'Natur','D');
                GLAccount."Debit/Credit"::Credit:
                    XMLDOMManagement.AddAttribute(Node,'Natur','A');
            ELSE
            TempErrorMessage.LogMessage(
                GLAccount,GLAccount.FIELDNO("Debit/Credit"),TempErrorMessage."Message Type"::Error,
                STRSUBSTNO(GLAccountTypeErr,GLAccount."Debit/Credit",GLAccount.RECORDID));
            END;
        UNTIL GLAccount.NEXT = 0;
    END ELSE
        TempErrorMessage.LogSimpleMessage(TempErrorMessage."Message Type"::Error,NoSATAccountDefinedErr);
    
        IF NOT TempErrorMessage.HasErrors(TRUE) THEN
            SaveXMLToClient(Document,Year,Month,'CT');
            TempErrorMessage.ShowErrorMessages(FALSE);
    END;
```

One could also do pre-processing in a function of its own, and only if the pre-processing results in no error messages of type "Error" would the processing continue.

## Ideas for improvement

By using this easy to use component, we have the possibility to extend this functionality going forward. A nice addition to the error message component would be the possibility to log the error messages persistently in a grouped manner, thereby allowing 3rd parties to see the issues the users bump into the most, or allowing 3rd parties to get an detailed insight into what happened, thereby enabling them to provide better support.



[anchor0]: /nav/w/designpatterns/124.journal-error-processing.aspx
[anchor1]: image001.png
[anchor2]: /nav/w/designpatterns/104.easy-update-of-setup-or-supplementary-information.aspx
[anchor3]: image003.png


[image0]: image001.png
[image1]: image003.png
