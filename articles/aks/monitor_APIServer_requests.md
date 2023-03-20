---
title: Query apiserver requests
description: Enable kube-audit to query the various types of requests to apiserver
ms.topic: conceptual
ms.owner: sprab
ms.contributors: sprab, sugan
---

## Overview

This article describes how to use Azure diagnostics to enable logging for kube-audit and monitor the requests to apiserver from various sources.

## Scenario
* Monitor the requests that are coming to the API Server.
* To find which apiserver pod is serving the request.
* Which user agent is sending the request.
* What is the http method for the calls.
* Number of calls from each source.

Since AKS control plane is managed by Microsoft, the Kubernetes Audit logging is not enabled by default.

> [!NOTE]
>
> Please note that there could be substantial cost involved once kube-audit logs are enabled.
> 
> Refer to Azure Monitoring under [Azure Pricing Calculater](https://azure.microsoft.com/en-au/pricing/calculator/) to estimate the costs involved.
> 
> Consider disabling kube-audit logging when not required.

## Enable Kubernetes Audit Logging:

### To enable kubernetes audit logs 

Go to Diagnostic settings under Monitoring
Click on + Add diagnostic setting



![Screenshot that shows how to enable diagnostics for aks cluster](https://user-images.githubusercontent.com/17014671/221720712-31409209-0860-4bd5-b6f6-39967d96eb4c.png)

### Diagnostic setting
1.  Give a name to the Diagnostic setting
2.  Select Kubernetes Audit under Categories
3.  Choose Send to Log Analytics workspace under Destination details
4.  Select the subscription for the Log Analytics workspace
5.  Choose the Log Analytics workspace to send the logs
6.  Save the settings


![Screenshot that shows how to enable diagnostics for aks cluster](https://user-images.githubusercontent.com/17014671/221721006-02f5f7f6-3e1c-40cc-a26a-9d24297a4235.png)


Once the kube-audit is enabled the data can be queried from the AzureDiagnostics table:


### Query the apiserver requests

```kusto
let starttime = datetime("2023-02-23T01:16:12.374Z");
let endtime = datetime("2023-02-24T12:16:12.374Z");
AzureDiagnostics
| where TimeGenerated between(starttime..endtime)
| where Category == "kube-audit"
| extend event = parse_json(log_s)
| extend HttpMethod = tostring(event.verb)
| extend User = tostring(event.user.username)
| extend Apiserver = pod_s
| extend SourceIP = tostring(event.sourceIPs[0])
| project TimeGenerated, Category, HttpMethod, User, Apiserver, SourceIP, OperationName, event
```

![Screenshot of the query results](https://user-images.githubusercontent.com/17014671/221722983-b20995df-338b-4d13-8b59-702e4d749890.png)

In this example we are querying the http requests originating from a SourceIP. The above query can be modified to project data in different dimensions.
