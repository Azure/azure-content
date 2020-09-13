---
title: What's new with Azure Arc enabled servers (preview) agent
description: This article has release notes for Azure Arc enabled servers (preview) agent. For many of the summarized issues, there are links to additional details.
ms.topic: conceptual
ms.date: 08/31/2020
---

# What's new with Azure Arc enabled servers (preview) agent

The Azure Arc enabled servers (preview) Connected Machine agent receives improvements on an ongoing basis. To stay up to date with the most recent developments, this article provides you with information about:

- The latest releases
- Known issues
- Bug fixes

## September 2020

Version: 1.0 (General Availability)

### Breaking Changes

- Support for preview agents (all versions older than 1.0) will be removed in a future service update. 
- Removed support for fallback endpoint under `.azure-automation.net`. If you have a proxy and have not white-listed the endpoint `*.his.arc.azure.com`  [here](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/azure-arc/servers/agent-release-notes.md) 
- If Connected Machine agent is installed on a virtual machine hosted in Azure, extensions cannot be installed or modified via the Arc for Servers resource ID. This is to avoid conflicting extension operations being performed from the virtual machine's Microsoft.Compute resource and Microsoft.HybridCompute resource. Use the Microsoft.Compute resource for the machine for all extension operations. 
- Name of Guest Configuration process has changed, from gcd to gcad on Linux, and gcservice to gcarcservice on Windows. 

### New functionality
- Added `azcmagent logs` command to collect information for support
- Added `azcmagent license` command to display EULA
- Added `azcmagent show --json` option to output agent state in easily parseable format
- Added flag in `azcmagent show` output to indicate if server is on a virtual machine hosted in Azure
- Added `azcmagent disconnect --force-local-only` to allow reset of local agent state when Azure service cannot be reached
- Added `azcmagent connect --cloud` option to support additional clouds. NB: only AzureCloud is supported at time of agent release, other clouds will be supported in future.
- Agent has been localized into Azure-supported languages.


### Fixes
- Improvements to connectivity check
- Fix issue with proxy server settings being lost when upgrading agent on Linux

## August 2020

Version: 0.11

- This release previously indicated support for Ubuntu 20.04. **Because some VM extensions do not support Ubuntu 20.04 we cannot support this version at this time.**
- Reliability improvements for extension deployments.

### Known issues

If you are using an older version of the Linux agent and configured it to use a proxy server, you need to reconfigure the proxy server setting after the upgrade. To do this, run `sudo azcmagent_proxy add {url of your proxy}`.

## Next steps

Before evaluating or enabling Arc enabled servers (preview) across multiple hybrid machines, review [Connected Machine agent overview](agent-overview.md) to understand requirements, technical details about the agent, and deployment methods.
