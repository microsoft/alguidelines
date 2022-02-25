---
title: "Custom Telemetry"
tags: ["AL","Telemetry"]
categories: ["Best Practice"]
---

_Created by Microsoft, Described by Arend-Jan Kauffmann_

## Description
With AL it is possible to emit custom telemetry signals to Azure Application Insights. There are a number of considerations that you should keep in mind when designing custom telemetry signals.

- Think about it as an API
- Naming conventions and telemetry schema
- Candidate data for telemetry
- How customers use telemetry
- Privacy

## Think about it as an API

Customers will build analytics and monitoring solutions on top of their telemetry data.

Therefore, signal must be treated as any other API

- documented
- versioned
- discoverable
- non-breaking

## Naming conventions and telemetry schema

To make it easy for the consumer of telemetry to work with the data, please

- use CamelCasing. This makes all fields in Application Insights look the same (signal logged through the AL LogMessage method will have "al" prefixed to dimension names.
- never use fields/custom dimension keys with spaces or special characters. This makes the KQL queries so much easier to write
- for custom dimensions, consider using prefixes that helps the telemetry consumer understand where the dimension is coming from (e.g. HttpStatusCode, SqlStatement, ...)

Consider always having a "message" field that expresses in human readable form what the telemetry event is about.
If you do, let message names follow the Object ActionInPastTense pattern
Some examples

- Web Service Called:
- Email attempt failed
- Authorization to environment succeeded

```al
local procedure ProcessHttpResponse(var Request: HttpRequestMessage; var Response: HttpResponseMessage)
var
    CustomTelemetryDimensions: Dictionary of [Text,Text];
begin
    if Response.HttpStatusCode <> 200 then begin
        CustomTelemetryDimensions.Add('Url', Request.GetRequestUri);
        CustomTelemetryDimensions.Add('HttpStatusCode', Format(Response.HttpStatusCode));
        CustomTelemetryDimensions.Add('ReasonPhrase', Response.ReasonPhrase);
        Session.LogMessage(
            'MyExt0001', 
            'Web service call failed', 
            Verbosity::Error, 
            DataClassification::SystemMetadata, 
            TelemetryScope::ExtensionPublisher, 
            CustomTelemetryDimensions);
    end;
end;
```

## Candidate data for telemetry

Telemetry must be actionable for the customer. Do not emit signals that they cannot act on (knowing about CPU performance counters on the database is useless if the partner cannot scale the database)

Also, note that customers pay for data ingestion. So be mindful to not flood their telemetry resources. Consider to use TelemetryScope::ExtensionPublisher by default and only use TelemetryScope::All in case the customer can also act on the data.

If you do not know where to start, consider using telemetry for deflection. In Dynamics 365 Business Central, they started with signal about authorization (successful/failed) to deflect support cases that was due to disabled users/wrong licenses.

## How customers use telemetry

The following are known scenarios for customer telemetry

- The tenant admin (typically an IT-pro) wants to troubleshoot a performance problem and they need more details than what is provided in the analytics reports in the admin center.
- The customer wants to analyze (and correct) errors happening in the environment (typically an IT-pro)
- The customer wants to analyze usage of features (typically an analytics user, maybe with BI experience)

Customers typically start in the Application Insights portal and then move on to use more advanced tools for analytics (KQL, Power BI, Excel, ...). Once they have seen the light, they will likely start alerting on telemetry using Azure Monitor Alerts or setting up Power Automate flows.

Business Central have developed a telemetry maturity model (based on the Gartner BI maturity model) for how organizations can evolve to use telemetry proactively in their business processes.

## Privacy

Telemetry must be privacy compliant.

For privacy reasons, events that have a DataClassification other than SystemMetadata aren't sent to Application Insight resources set up on the tenant. During development of your extension, it's good practice to have a privacy review of the use of LOGMESSAGE calls to ensure that customer data isn't mistakenly leaked into Application Insights resources.