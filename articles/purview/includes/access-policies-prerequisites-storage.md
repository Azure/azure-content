---
author: inward-eye
ms.author: vlrodrig
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: include
ms.date: 11/10/2022
ms.custom: references_regions
---

#### Configure the subscription where the Azure Storage account resides for policies from Microsoft Purview
To enable Microsoft Purview to manage policies for one or more Azure Storage accounts, execute the following PowerShell commands in the subscription where you'll deploy your Azure Storage account. These PowerShell commands will enable Microsoft Purview to manage policies on all **newly created** Azure Storage accounts in that subscription.

If you’re executing these commands locally, be sure to run PowerShell as an administrator.
Alternatively, you can use the [Azure Cloud Shell](../../cloud-shell/overview.md) in the Azure portal: [https://shell.azure.com](https://shell.azure.com).

```powershell
# Install the Az module
Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force
# Login into the subscription
Connect-AzAccount -Subscription <SubscriptionID>
# Register the feature
Register-AzProviderFeature -FeatureName AllowPurviewPolicyEnforcement -ProviderNamespace Microsoft.Storage
```

If the output of the last command shows *RegistrationState* as *Registered*, then your subscription is enabled for access policies.
If the output is *Registering*, wait at least 10 minutes, and then retry the command. **Do not continue unless the RegistrationState shows as *Registered***.

>[!NOTE]
> Storage accounts must meet the following requirements to enforce access policies published from Microsoft Purview.
> - Storage account versions >= 81.x.x.

#### Region support for Azure Storage accounts
Existing Azure Storage account(s) are supported in one of the regions listed below. You can also create a new one by [following this guide(../../storage/common/storage-account-create.md).

Currently, Microsoft Purview access policies can only be enforced in the following Azure Storage regions:
- East US
- East US2
- South Central US
- West US
- West US2
- Canada Central
- North Europe
- West Europe
- France Central
- UK South
- East Asia
- Southeast Asia
- Japan East
- Japan West
- Australia East

The following regions support only Azure Storage accounts created in the subscription **after** the feature *AllowPurviewPolicyEnforcement* is *Registered*.
- West US
- East Asia
- Japan East
- Japan West
