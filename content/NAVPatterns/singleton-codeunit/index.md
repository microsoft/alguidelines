+++
title = "singleton-codeunit.md"
weight = 1100
+++
## Singleton Codeunit

_By Bogdana Botez at Microsoft Development Center Copenhagen   
_

_[![ ][image0]][anchor0]  
_

**Problem**: In some situations, global state needs to be preserved at runtime throughout a session.

There are functionality areas in NAV where centralized application management code is needed, like for example managing permissions, notifications, the debugger etc. State needs to be preserved across calls to the management codeunit.

For example:

* The debugger needs to remember the session which is being debugged
* The permission manager has a testability function, where it can be set to emulate that it's running as a SaaS (Software As A Service -- the cloud) platform, even when the tests run in a "on-premise" local lab.

Take for example the following test of Azure ML (Azure Machine Learning) integration with NAV.

Context: Azure Machine Learning services are paid. However, Dynamics NAV includes a monthly pre-paid quota of Azure ML, which can be used for free by the users. There is an upper limit to this quota, and when it is reached, Azure ML services are turned off until the next month starts and a new quota becomes available for consumption.

The test below checks that, when the monthly quota is exceeded, then the function IsAzureMLLimitReached returns TRUE.

    \[Test\]
    PROCEDURE AzureMLProcessingTimeExceedsLimit@11();
    VAR  
    AzureMachineLearningUsage@1004 : Record 2002;
    ProcessingTime@1000 : Decimal;  
    BEGIN
    // \[SCENARIO\] Azure Machine Learning Processing time exceeds AzureML limit  
    // \[GIVEN\] AzureMachineLearningUsage \> 0
    Initialize; // calls PermissionManager.SetTestabilitySoftwareAsAService(TRUE);
    ProcessingTime := LibraryRandom.RandDec(1000,2);
    AzureMachineLearningUsage.IncrementTotalProcessingTime(ProcessingTime);  
    // \[WHEN\] When IsAzureMLLimitReached is invoked with Limit more than Processing time
    // \[THEN\] HasAzureLimitReached returns TRUE
    Assert.IsTrue(AzureMachineLearningUsage.IsAzureMLLimitReached(ProcessingTime - 1),
    'HasAzureLimitReached returns wrong value when Processing time exceeds Limit.');
    PermissionManager.SetTestabilitySoftwareAsAService(FALSE);  
    END;

The figure below explains what happens when the Permission Manager is not a singleton. When it is invoked from different places (first from the test, second from the production code), then different instances of the Permission Manager will fire up and answer. In detail:

1. The test calls Initialize which sets SaaS=TRUE in codeunit Permission Manager
2. The test calls into production code to validate it works as expected. It calls AzureMachineLearningUsage codeunit to find out if the monthly quota has been reached. The function IsAzureMLLimitReached in AzureMachineLearningUsage codeunit is designed only for SaaS. If the code doesn't run in SaaS, then it always returns FALSE.
3. Therefore, a call to PermissionManager is made, to find out if the environment is SaaS.
4. However, a different instance of Permission Manager is reached -- and instance where SaaS was never set to TRUE. This is a mistake -- the test intended to simulate SaaS, but the state it set in the beginning is not reachable from production code.
5. The production code will assess (wrongly) that it's not running SaaS, and say that the Azure ML limit has not been reached (incorrect -- and the test fails).

[![ ][image1]][anchor1]

****

**Solution:** restrict the number of instantiations of a codeunit to only one, by setting the codeunit property **SingleInstance** to **Yes**.

Returning to the previous example, let's analyze the case when the codeunit Permission Manager is a singleton codeunit:

[![ ][image2]][anchor2]

When the codeunit Permission Manager is a singleton, then no matter from where it is invoked, the same instance will be reached. Therefore, the status set by the test (SaaS = TRUE) will be reachable from the production code, and the test will pass, as seen in the figure below.

**[![ ][image3]][anchor3]  
**

**Consequences**

1\. Use Singleton Codeunit with care and only when there is no other solution. Preserving a global state could often enough be more harmful than useful. One risk is that tests might fail apparently non-deterministically.

For example, a problem we have met in the development team for Madeira release, was that the singleton codeunit function PermissionManager.SetSoftwareAsAService(TRUE) is often used to emulate and test SaaS conditions. However, if a test 'forgets' to reset the state to default (FALSE), then another codeunit which is not supposed to emulate SaaS, will suddenly run as SaaS and will fail. Even if the test has code that resets the state to FALSE, this code might never be reached because of an earlier failure or other error in the test which would stop execution.

2\. The singleton codeunit is only "alive" for the current session. If the user logs out, the old session is closed and the singleton cleared out so any values stored in the old session's singleton will be lost when the session was closed. When the user logs in again, a new session (with a new fresh instance of the singleton) will be created.

**NAV Usages**

Most of the usages in NAV refer to the so-called "management codeunits". The management codeunits are needed to run, in a centralized way, various modular parts of the application (features), like the CRM integration, Permissions, Workflows etc. Some of the **Singleton Codeunits** in NAV are listed below:

* Codeunit 423 Change Log Management
* Codeunit 1503 Workflow Record Management
* Codeunit 1511 Notification Lifecycle Mgt.
* Codeunit 1629 Office Attachment Manager
* Codeunit 1632 Office Error Engine
* Codeunit 5150 Integration Management
* Codeunit 9002 Permission Manager
* Etc.

**Note:** while the object-oriented **Singleton** pattern can restrict the number of instantiations of the singleton to an integer n \> 0, in Dynamics NAV the **Singleton Codeunit** can only have n=1\.

__



[anchor0]: Singleton-Codeunit.png
[anchor1]: Singleton-Codeunit-_2D00_-example-_2D00_-bad.PNG
[anchor2]: 2313.Singleton-Codeunit-_2D00_-CSIDE-SingleInstance-property.PNG
[anchor3]: Singleton-Codeunit-_2D00_-example-_2D00_-good.PNG


[image0]: Singleton-Codeunit.png
[image1]: Singleton-Codeunit-_2D00_-example-_2D00_-bad.PNG
[image2]: 2313.Singleton-Codeunit-_2D00_-CSIDE-SingleInstance-property.PNG
[image3]: Singleton-Codeunit-_2D00_-example-_2D00_-good.PNG
