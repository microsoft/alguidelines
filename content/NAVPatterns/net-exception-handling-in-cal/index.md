+++
title = "net-exception-handling-in-cal.md"
weight = 860
+++
_by Mostafa Balat, Microsoft Development Center Copenhagen_

## Abstract

When there is a need to use .NET classes within C/AL, one of the main challenges is to handle the exceptions the methods of these .NET classes may throw. Eventually, if not handled, they will basically bubble up as runtime errors, halting the current operation a user is doing without having a chance to properly display errors in a user-friendly format.

## Description

Using the .NET classes in order to extend NAV's functionality usually triggers the need to create an add-on assembly. This is a pretty powerful approach and opens the door for empowering NAV with new and extra functionality while harnessing the full power of .NET.

For example, integration with a Web service into NAV can be done to extend NAV's functionality or benefit from a service model offered through a 3rd party. To do so, it is possible to write a .NET add-in to handle the required bi-directional communication between NAV and the Web service. Alternatively, the implementation itself can be done in C/AL, with no add-in dependency. The latter option simplifies customization, deployment and upgradeability. Additionally, it builds up on the knowledge NAV developers have with C/AL programming.

On the other hand, not using an add-in exposes NAV to runtime errors due to unhandled exceptions that get thrown at different levels. The first is the communication layer, in which HTTP requests and responses are exchanged. The second is the business logic layer, at which the content of the requests and response is being prepared and groomed using XML Elements and being retrieved or edited based on the respective XPaths.

### When to Use It

When .NET classes are used, they may throw exceptions upon failure. Some of these exceptions cannot pre-checked (e.g. like existence of a file on disk) and will only be figured out at runtime. Eventually, to present the error reason to a user and explain what needs to be done to address it, the exception needs to be handled gracefully. This also protects the client for unexpected crashes that may deteriorate the user experience.

### Diagram

[![ ][image0]][anchor0]

## Usage

A Try-Catch-Finally statement does not exist in C/AL. The alternative is to run the code consuming .NET objects within a codeunit, and handle the runtime errors, as follows:

1. Write the code that uses the .NET classes in a codeunit.
2. Run the codeunit using **CODEUNIT.RUN** or the Run function on a codeunit variable.
3. Handle the return value of the **OnRun** trigger for the codeunit within an **IF** statement.
4. Write the exception handling code in a function, and call it when the return value for **OnRun** is **FALSE**.
5. The exception handling function should determine which exception to handle, and pass this over to **COD1291 DotNet Exception Handler** codeunit.

When an exception is thrown, it is already wrapped up within an NAV exception. This means the real exception to look for is retrievable through the **InnerException** property of the NAV exception. Then, next step would be to properly determine the type of that exception, and act accordingly. The **COD1291 DotNet Exception Handler** codeunit takes care of looping through the nested levels of inner exceptions, digging for an exception with the expected type. If found, it is retrieved; otherwise, the main (outer) exception's message is retrieved.

## NAV Specific Example

### Overview

The exception handling pattern is implemented in the integration with a web service for bank file format conversion. Within the respective "External Data Handling Codeunit" for that web service, communication through SOAP requests is required. Eventually, the HTTP web request and response .NET classes are used for that purpose.

For instance, if a failure to establish a connection to the web service due to lack of Internet access, a **WebException** is thrown with the relevant error details. **COD1290 Web Service Mgt.** codeunit wraps up the required functionality to interact with a web service in C/AL, handling all the required plumping work to send web requests, receive web responses, and extract valid and error details from the web response.

### Code Sample

The "External Data Handling Codeunit" is a consumer of **COD1290 Web Service Mgt.** codeunit. If a runtime exception occurs, it gets handled as follows:

    LOCAL PROCEDURE SendDataToConversionService@1(VAR PaymentFileTempBlob@1003 : Record 99008535;BodyTempBlob@1004 : Record 99008535;PostingExch@1007 : Record 1220);
    VAR
    BankDataConvServiceSetup@1000 : Record 1260;
    WebServiceRequestMgt@1001 : Codeunit 1290;
    BodyInStream@1005 : InStream;
    ResponseInStream@1002 : InStream;
    BEGIN
    IF NOT BodyTempBlob.Blob.HASVALUE THEN
    ERROR(NoRequestBodyErr);
    PrepareSOAPRequestBody(BodyTempBlob);
    COMMIT;
    BankDataConvServiceSetup.GET;
    BodyTempBlob.Blob.CREATEINSTREAM(BodyInStream);
    WebServiceRequestMgt.SetGlobals(BodyInStream,
    BankDataConvServiceSetup."Service URL",BankDataConvServiceSetup."User Name",BankDataConvServiceSetup.GetPassword);
    IF NOT WebServiceRequestMgt.RUN THEN
    WebServiceRequestMgt.ProcessFaultResponse;
    WebServiceRequestMgt.GetResponseContent(ResponseInStream);
    CheckIfErrorsOccurred(ResponseInStream,PostingExch);
    ReadContentFromResponse(PaymentFileTempBlob,ResponseInStream);
    END;

    PROCEDURE ProcessFaultResponse@15();
    VAR
    XMLDOMMgt@1006 : Codeunit 6224;
    DotNetExceptionHandler@1000 : Codeunit 1291;
    WebException@1005 : DotNet "'System, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089'.System.Net.WebException";
    WebExceptionStatus@1004 : DotNet "'System, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089'.System.Net.WebExceptionStatus";
    XmlDoc@1003 : DotNet "'System.Xml, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089'.System.Xml.XmlDocument";
    HttpWebResponseError@1007 : DotNet "'System, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089'.System.Net.HttpWebResponse";
    HttpStatusCode@1008 : DotNet "'System, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089'.System.Net.HttpStatusCode";
    ResponseInputStream@1002 : InStream;
    BEGIN
    DotNetExceptionHandler.Collect;
    IF NOT DotNetExceptionHandler.CastToType(WebException,GETDOTNETTYPE(WebException)) THEN
    DotNetExceptionHandler.Rethrow;
    IF NOT WebException.Status.Equals(WebExceptionStatus.ProtocolError) THEN
    ERROR(WebException.Message);
    ResponseInputStream := WebException.Response.GetResponseStream;
    DebugLogStreamToTempFile(ResponseInputStream,'WebExceptionResponse',TempDebugLogTempBlob);
    HttpWebResponseError := WebException.Response;
    IF NOT (HttpWebResponseError.StatusCode.Equals(HttpStatusCode.Found) OR
    HttpWebResponseError.StatusCode.Equals(HttpStatusCode.InternalServerError))
    THEN
    ERROR(WebException.Message);
    XmlDoc := XmlDoc.XmlDocument;
    XmlDoc.Load(ResponseInputStream);
    ERROR(XMLDOMMgt.FindNodeTextWithNamespace(XmlDoc.DocumentElement,FaultStringXmlPathTxt,'soap',SoapNamespaceTxt));
    END;

    OBJECT Codeunit 1291 DotNet Exception Handler
    {
    OBJECT-PROPERTIES
    {
    Date=;
    Time=;
    Version List=;
    }
    PROPERTIES
    {
    OnRun=BEGIN
    END;
    }
    CODE
    {
    VAR
    OuterException@1000 : DotNet "'mscorlib'.System.Exception";
    PROCEDURE Catch@3(VAR Exception@1002 : DotNet "'mscorlib'.System.FormatException";Type@1007 : DotNet "'mscorlib'.System.Type");
    BEGIN
    Collect;
    IF NOT CastToType(Exception,Type) THEN
    Rethrow;
    END;
    PROCEDURE Collect@1();
    BEGIN
    OuterException := GETLASTERROROBJECT;
    END;
    PROCEDURE TryCastToType@5(Type@1000 : DotNet "'mscorlib'.System.Type") : Boolean;
    VAR
    Exception@1001 : DotNet "'mscorlib'.System.FormatException";
    BEGIN
    EXIT(CastToType(Exception,Type));
    END;
    PROCEDURE CastToType@2(VAR Exception@1002 : DotNet "'mscorlib'.System.FormatException";Type@1007 : DotNet "'mscorlib'.System.Type") : Boolean;
    BEGIN
    Exception := OuterException;
    REPEAT
    IF Type.Equals(Exception.GetType()) THEN
    EXIT(TRUE);
    Exception := Exception.InnerException;
    UNTIL ISNULL(Exception);
    EXIT(FALSE);
    END;
    PROCEDURE Rethrow@4();
    BEGIN
    IF NOT ISNULL(OuterException.InnerException) THEN
    ERROR(OuterException.InnerException.Message);
    ERROR(OuterException.Message);
    END;
    BEGIN
    END.
    }
    }

## NAV Usages

The DotNet Exception Handler codeunit has been used for the Web service integration required for:

1. Payment Export from the Payment Journal for creating bank-specific payment files.
2. Bank Statement Import on the Bank Acc. Reconciliation card for importing the content of bank-specific statements.
3. Bank name lookup on the Bank Account card for dynamically identifying the format to use to generate a bank-specific payment file.

## Ideas for Improvement

Extend the language support in C/AL to provide a built-in Try-Catch-Finally statement, similar to .NET languages.

## Consequences

When this pattern should not be used: avoid nesting of codeunits. To properly handle the exceptions, you need to use the codeunit as an atomic piece of functionality that may pass or fail without using Codeunit.Run internally.



[anchor0]: 1856.DotNet_5F00_Exception_5F00_Handling_5F00_in_5F00_CAL_5F00_Diagram.png


[image0]: 1856.DotNet_5F00_Exception_5F00_Handling_5F00_in_5F00_CAL_5F00_Diagram.png
