---
ms.custom: devx-track-azurepowershell
---
## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/dotnet/).
- Install the [Azure Az PowerShell Module](/powershell/azure/)
- Create an [Email Communication Service](/azure/communication-services/quickstarts/email/create-email-communication-resource).

## Create a Domain resource

To create a Domain resource, Sign into your Azure account by using the ```Connect-AzAccount``` using the following command and provide your credentials.

```PowerShell
PS C:\> Connect-AzAccount
```

First, make sure to install the Azure Communication Services module ```Az.Communication``` using the following command.

```PowerShell
PS C:\> Install-Module Az.Communication
```

Run the following command to create the Azure managed domain resource:

```PowerShell
PS C:\> New-AzEmailServiceDomain -ResourceGroupName ContosoResourceProvider1 -EmailServiceName ContosoEmailServiceResource1 -Name AzureManagedDomain -DomainManagement AzureManaged
```

You can configure your Domain resource with the following options:

* The [resource group](../../../../azure-resource-manager/management/manage-resource-groups-powershell.md)
* The name of the Email Communication Services resource.
* The name of the Domain resource:
	* For Azure domains, the name should be 'AzureManagedDomain'.
* The name of the Domain management.
	* For Azure domains, the name should be 'AzureManaged'.

In the next step, you can assign tags to the domain resource. Tags can be used to organize your Domain resources. See the [resource tagging documentation](../../../../azure-resource-manager/management/tag-resources.md) for more information about tags.

## Manage your Domain resource

To add tags to your Domain resource, run the following commands. You can target a specific subscription as well.

```PowerShell
PS C:\> Update-AzEmailServiceDomain -Name AzureManagedDomain -EmailServiceName ContosoEmailServiceResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"}

PS C:\> Update-AzEmailServiceDomain -Name AzureManagedDomain -EmailServiceName ContosoEmailServiceResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"} -SubscriptionId SubscriptionID
```

To list all of your Domain Resources in a given Email Communication Service use the following command:

```PowerShell
PS C:\> Get-AzEmailServiceDomain -EmailServiceName ContosoEmailServiceResource1 -ResourceGroupName ContosoResourceProvider1
```

To list all the information on a given domain resource use the following command:

```PowerShell
PS C:\> Get-AzEmailServiceDomain -Name AzureManagedDomain -EmailServiceName ContosoEmailServiceResource1 -ResourceGroupName ContosoResourceProvider1
```

If you want to clean up and remove a Domain resource, You can delete your Domain resource by running the command below.

```PowerShell
PS C:\> Remove-AzEmailServiceDomain -Name AzureManagedDomain -EmailServiceName ContosoEmailServiceResource1 -ResourceGroupName ContosoResourceProvider1
```
