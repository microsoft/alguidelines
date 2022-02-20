+++
title = "Create URLs to NAV Clients"
weight = 410
+++
_By Mike Borg Cardona and Bogdana Botez at Microsoft Development Center Copenhagen_

## Abstract

This article illustrates NAV platform functionality to be used by C/AL developers.

The URL builder function, GETURL, is released in Microsoft Dynamics NAV 2013 R2 to reduce coding time for developers who need to create various URL strings to run application objects in either the win client, the web client, or on web services. In addition, the GETURL function makes multitenancy features more transparent to C/AL developers.

## Description

Ever had to construct win client URLs like the one below?

dynamicsnav://myserver:7046/myInstance/myCompany/runpage?page=26

Today, Microsoft Dynamics NAV also provides a web client. This means that you must update your code to construct web client URLs too. What about multitenancy? The URL Builder should know if it is running in a multitenant setup and it should know how to choose the right tenant. What about maintaining this code?

The good news is that GETURL has been introduced to handle all URL building for you.

GETURL automatically handles:

* Multitenancy
* Correct URL format for each client
* Publicly accessible hostnames.

## Usage

The format is:

[String :=] GETURL(ClientType[, Company][, Object Type][, Object Id][, Record])

Where:

* **Client Type** can be: Current, Default, Windows, Web, SOAP, or OData. This enables a range of scenarios for the C/AL developer, such as moving to the web client without changing code to decide where the URL should point to. This is done either by setting Client Type to Current, and just ensuring that web is used to invoke the link creation, or by setting Client Type to Default and changing its value to Web when it is ready to move to the web platform.
* **Object Type** and **Object ID** define the type of the application object to run (Table, Page, Report, Codeunit, Query, or XMLport) and its ID.
* **Record** specifies the actual data to run the URL on, such as:

```al
Vendor.GET("Account No.");

GETURL(CLIENTTYPE:WEB,COMPANYNAME, OBJECTTYPE::Page,27,Vendor)
```

**Note**: It is currently not possible to set filters on the record that you sent as a last parameter to the GETURL function. However, it is possible to write your own code to compute and append the filter string to the URL that is created by the GETURL function.

The server name and instance are extracted automatically by GETURL and do not need to be specified by the C/AL developer. Furthermore, the multitenancy setup is transparent to the C/AL developer. No multitenancy parameters are specified when you call GETURL, because the function knows from the server setup if it is running in a multitenant environment and if so, it will add a string like "&tenant=MyTenant" to the URL.

## When to Use

The GETURL function can generally be used every time a URL must be created. The following are some scenarios where the function is particularly useful.

* Document approvals. For more information, see the "NAV Usage Example" section.
* Reports containing drill-down links. (Beware of the resource cost of adding a new URL element to the Report dataset.)
* When planning to write code for, or migrate to, various display targets (Microsoft Dynamics NAV Windows client, Microsoft Dynamics NAV web client, Microsoft Dynamics NAV web services) without having to explicitly specify which client to use.

## Examples of Usage

The following are examples of calls to GETURL and their corresponding return value:

Command    | URL
--------|------
GETURL(CLIENTTYPE::Win)   | dynamicsnav://MyServer:7046/DynamicsNAV71//
GETURL(CLIENTTYPE::Web)   | https://navwebsrvr:443/DynamicsNAV71_Instance1/Webclient
GETURL(CLIENTTYPE::OData) | http://MyServer:7048/DynamicsNAV71/OData
GETURL(CLIENTTYPE::SOAP)  | http://MyServer:7047/DynamicsNAV71/WS/Services
GETURL(CLIENTTYPE::Current) ie. When running this code on a Win client session | dynamicsnav://MyServer:7046/DynamicsNAV71//
GETURL(CLIENTTYPE::Default) ie. When the Server config key DefaultClient is set to Windows | dynamicsnav://MyServer:7046/DynamicsNAV71//
GETURL(CLIENTTYPE::Windows,COMPANYNAME) | dynamicsnav://MyServer:7046/DynamicsNAV71/CRONUS/
GETURL(CLIENTTYPE::Windows,'') | dynamicsnav://MyServer:7046/DynamicsNAV71//
GETURL(CLIENTTYPE::Windows,'NONEXISTING Corp') | dynamicsnav://MyServer:7046/DynamicsNAV71/NONEXISTING Corp/
GETURL(CLIENTTYPE::Web,COMPANYNAME) | https://navwebsrvr:443/DynamicsNAV71_Instance1/Webclient?company=CRONUS
GETURL(CLIENTTYPE::Web,'') | https://navwebsrvr:443/DynamicsNAV71_Instance1/Webclient
GETURL(CLIENTTYPE::Web,'NONEXISTING Corp') | https://navwebsrvr:443/DynamicsNAV71_Instance1/Webclient?company=NONEXISTING Corp
GETURL(CLIENTTYPE::OData,COMPANYNAME) | http://MyServer:7048/DynamicsNAV71/OData/Company('CRONUS')
GETURL(CLIENTTYPE::OData,'') | http://MyServer:7048/DynamicsNAV71/OData
GETURL(CLIENTTYPE::OData,'NONEXISTING Corp') | http://MyServer:7048/DynamicsNAV71/OData/Company('NONEXISTING Corp')
GETURL(CLIENTTYPE::SOAP,COMPANYNAME) | http://MyServer:7047/DynamicsNAV71/WS/CRONUS/Services
GETURL(CLIENTTYPE::SOAP,'') | http://MyServer:7047/DynamicsNAV71/WS/Services
GETURL(CLIENTTYPE::SOAP,'NONEXISTING Corp') | http://MyServer:7047/DynamicsNAV71/WS/NONEXISTING Corp/Services
GETURL(CLIENTTYPE::Windows,COMPANYNAME,OBJECTTYPE::Table,27) | dynamicsnav://MyServer:7046/DynamicsNAV71/CRONUS/runtable?table=27
GETURL(CLIENTTYPE::Windows,COMPANYNAME,OBJECTTYPE::Page,27) | dynamicsnav://MyServer:7046/DynamicsNAV71/CRONUS/runpage?page=27
GETURL(CLIENTTYPE::Windows,COMPANYNAME,OBJECTTYPE::Report,6) | dynamicsnav://MyServer:7046/DynamicsNAV71/CRONUS/runreport?report=6
GETURL(CLIENTTYPE::Windows,COMPANYNAME,OBJECTTYPE::Codeunit,5065) | dynamicsnav://MyServer:7046/DynamicsNAV71/CRONUS/runcodeunit?codeunit=5065
GETURL(CLIENTTYPE::Windows,COMPANYNAME,OBJECTTYPE::Query,9150) | dynamicsnav://MyServer:7046/DynamicsNAV71/CRONUS/runquery?query=9150
GETURL(CLIENTTYPE::Windows,COMPANYNAME,OBJECTTYPE::XmlPort,5150) | dynamicsnav://MyServer:7046/DynamicsNAV71/CRONUS/runxmlport?xmlport=5150
GETURL(CLIENTTYPE::OData,COMPANYNAME,OBJECTTYPE::Page,27) ie. When the Web Service is published | http://MyServer:7048/DynamicsNAV71/OData/Company('CRONUS')/PAG27Vendors
GETURL(CLIENTTYPE::OData,COMPANYNAME,OBJECTTYPE::Query,9150) ie. When the Web Service is published | http://MyServer:7048/DynamicsNAV71/OData/Company('CRONUS')/QUE9150MyCustomers
GETURL(CLIENTTYPE::SOAP,COMPANYNAME,OBJECTTYPE::Page,27)Â ie. When the Web Service is published | http://MyServer:7047/DynamicsNAV71/WS/CRONUS/Page/PAG27Vendors
GETURL(CLIENTTYPE::SOAP,COMPANYNAME,OBJECTTYPE::Codeunit,5065) ie. When the Web Service is published | http://MyServer:7047/DynamicsNAV71/WS/CRONUS/Codeunit/COD5065EmailLogging
GETURL(CLIENTTYPE::Windows,COMPANYNAME,OBJECTTYPE::Page,27,record) List Page | dynamicsnav://MyServer:7046/DynamicsNAV71/CRONUS/runpage?page=27&bookmark=23;FwAAAAJ7/0kAQwAxADAAMwAw
GETURL(CLIENTTYPE::Windows,COMPANYNAME,OBJECTTYPE::Page,26,record) Card Page | dynamicsnav://MyServer:7046/DynamicsNAV71/CRONUS/runpage?page=26&bookmark=23;FwAAAAJ7/0kAQwAxADAAMwAw
GETURL(CLIENTTYPE::Web,COMPANYNAME,OBJECTTYPE::Page,27,record) List Page | https://navwebsrvr:443/DynamicsNAV71_Instance1/Webclient?company=CRONUS&page=27&bookmark=23;FwAAAAJ7/0kAQwAxADAAMwAw
GETURL(CLIENTTYPE::Web,COMPANYNAME,OBJECTTYPE::Page,26,record) Card Page | https://navwebsrvr:443/DynamicsNAV71_Instance1/Webclient?company=CRONUS&page=26&bookmark=23;FwAAAAJ7/0kAQwAxADAAMwAw
GETURL(CLIENTTYPE::OData,COMPANYNAME,OBJECTTYPE::Page,27,record) | http://MyServer:7048/DynamicsNAV71/OData/Company('CRONUS')/PAG27Vendors('IC1030')
GETURL(CLIENTTYPE::Web,COMPANYNAME,OBJECTTYPE::Page,27) | https://navwebsrvr:443/DynamicsNAV71_Instance1/Webclient?company=CRONUS&page=27
GETURL(CLIENTTYPE::Web,COMPANYNAME,OBJECTTYPE::Report,6) | https://navwebsrvr:443/DynamicsNAV71_Instance1/Webclient?company=CRONUS&report=6

If the GETURL function is called with invalid parameters, it will return an empty string. In that case, you can find the related error text by calling the GETLASTERRORTEXT function.

Function Call | Error Message
--------|------
GETURL(CLIENTTYPE::Web,COMPANYNAME,OBJECTTYPE::Table,27) | The specified object type parameter for the GetUrl function is not valid.
GETURL(CLIENTTYPE::Web,COMPANYNAME,OBJECTTYPE::Codeunit,5065) | The specified object type parameter for the GetUrl function is not valid.
GETURL(CLIENTTYPE::Web,COMPANYNAME,OBJECTTYPE::Query,9150) | The specified object type parameter for the GetUrl function is not valid.
GETURL(CLIENTTYPE::Web,COMPANYNAME,OBJECTTYPE::XmlPort,5150) | The specified object type parameter for the GetUrl function is not valid.
GETURL(CLIENTTYPE::OData,COMPANYNAME,OBJECTTYPE::Table,27) | The specified object type parameter for the GetUrl function is not valid.
GETURL(CLIENTTYPE::OData,COMPANYNAME,OBJECTTYPE::Page,27) | The Page object, 27, that is specified for the GetUrl function has not been published in the Web Services table.
GETURL(CLIENTTYPE::OData,COMPANYNAME,OBJECTTYPE::Report,6) | The specified object type parameter for the GetUrl function is not valid.
GETURL(CLIENTTYPE::OData,COMPANYNAME,OBJECTTYPE::Codeunit,5065) | The specified object type parameter for the GetUrl function is not valid.
GETURL(CLIENTTYPE::OData,COMPANYNAME,OBJECTTYPE::Query,9150) | The Query object, 9150, that is specified for the GetUrl function has not been published in the Web Services table.
GETURL(CLIENTTYPE::OData,COMPANYNAME,OBJECTTYPE::XmlPort,5150) | The specified object type parameter for the GetUrl function is not valid.
GETURL(CLIENTTYPE::SOAP,COMPANYNAME,OBJECTTYPE::Table,27) | The specified object type parameter for the GetUrl function is not valid.
GETURL(CLIENTTYPE::SOAP,COMPANYNAME,OBJECTTYPE::Page,27) | The Page object, 27, that is specified for the GetUrl function has not been published in the Web Services table.
GETURL(CLIENTTYPE::SOAP,COMPANYNAME,OBJECTTYPE::Report,6) | The specified object type parameter for the GetUrl function is not valid.
GETURL(CLIENTTYPE::SOAP,COMPANYNAME,OBJECTTYPE::Codeunit,5065) | The Codeunit object, 5065, that is specified for the GetUrl function has not been published in the Web Services table.
GETURL(CLIENTTYPE::SOAP,COMPANYNAME,OBJECTTYPE::Query,9150) | The specified object type parameter for the GetUrl function is not valid.
GETURL(CLIENTTYPE::SOAP,COMPANYNAME,OBJECTTYPE::XmlPort,5150) | The specified object type parameter for the GetUrl function is not valid.
GETURL(CLIENTTYPE::SOAP,COMPANYNAME,OBJECTTYPE::Page,27,record) | You cannot specify a record parameter for the GetUrl function when the object type is SOAP

## NAV Specific Example

The following example shows how to use the GETURL function in codeunit 440 to ensure that the notification mail in Document Approvals can link to both the Microsoft Dynamics NAV Windows client and the Microsoft Dynamics NAV web client:

[![ ][image0]][anchor0]

This resulting UI looks as follows.

[![ ][image1]][anchor1]

The first link opens the approval document in the Microsoft Dynamics NAV Windows client. The second link (Web view) opens the same document in the Microsoft Dynamics NAV web client.



[anchor0]: 1778.url1.jpg
[anchor1]: 7802.url2.jpg


[image0]: 1778.url1.jpg
[image1]: 7802.url2.jpg
