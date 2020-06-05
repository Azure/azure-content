---
title: How to create Guest Configuration policies from Group Policy Baseline for Windows
description: Learn how to publish an Azure Policy Guest Configuration policy from Group Policy Baseline for Windows.
ms.date: 06/05/2020
ms.topic: how-to
---
# How to Publish Guest Configuration policies from Group Policy Baseline for Windows

Before creating custom policy definitions, it's a good idea to read the conceptual overview information at the page [Azure Policy Guest Configuration](../concepts/guest-configuration.md). To learn about creating custom Guest Configuration policies for Linux, see the page [How to create Guest Configuration policies for Linux](./guest-configuration-create-linux.md). To learn about creating custom Guest Configuration policies for Windows, see the page [How to create Guest Configuration policies for Windows](./guest-configuration-create.md). 

When auditing Windows, Guest Configuration uses a [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) resource module to create the configuration file. The DSC configuration defines the condition that the machine should be in. If the evaluation of the configuration fails, the policy effect **auditIfNotExists** is triggered and the machine is considered **non-compliant**.

[Azure Policy Guest Configuration](../concepts/guest-configuration.md) can only be used to audit settings inside machines. Remediation of settings inside machines isn't yet available. Use the following actions to create your own configuration for validating the state of an Azure or non-Azure machine.

> [!IMPORTANT]
> Custom policies with Guest Configuration is a Preview feature.
>
> The Guest Configuration extension is required to perform audits in Azure virtual machines.
> To deploy the extension at scale across all Windows machines, assign the following policy definitions:
>   - [Deploy prerequisites to enable Guest Configuration Policy on Windows VMs.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)

The DSC community has published tooling to convert exported Group Policy templates to DSC format. By using this tool together with Guest Configuration Cmdlet, you can convert Windows Group Policy content and package/publish it for Azure Policy to audit. For details about using the tool, see the article [Quickstart: Convert Group Policy into DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart). 

In this guide, we walk through the complete process of starting from a Group Policy to publishing an Azure Guest Configuration Policy. The walkthrough specifically outlines the process of converting the Server 2019 baseline GPO, but the same process can be applied to other group policy objects.  

## Install Server 2019 Baseline and Related PowerShell Modules

To install the **DSC**, **GuestConfiguration**, **Baseline Management**, and related Azure modules in PowerShell:

1. From a PowerShell prompt, run the following command:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module az.resources, az.policyinsights, az.storage, guestconfiguration, gpregistrypolicyparser, securitypolicydsc, auditpolicydsc, baselinemanagement -scope currentuser -Repository psgallery -AllowClobber
   ```

2. Create a directory for and download the Server 2019 Baseline from the Windows Security Compliance toolkit:

   ```azurepowershell-interactive
   # Download the 2019 Baseline files from https://docs.microsoft.com/en-us/windows/security/threat-protection/security-compliance-toolkit-10
   mkdir 'C:\git\policyfiles\downloads'
   Invoke-WebRequest -Uri 'https://download.microsoft.com/download/8/5/C/85C25433-A1B0-4FFA-9429-7E023E7DA8D8/Windows%2010%20Version%201909%20and%20Windows%20Server%20Version%201909%20Security%20Baseline.zip' -Out C:\git\policyfiles\downloads\Server2019Baseline.zip
   Get-Command -Module 'GuestConfiguration'
   ```

3. Unblock and expand the downloaded Server 2019 Baseline:

   ```azurepowershell-interactive
   Unblock-File C:\git\policyfiles\downloads\Server2019Baseline.zip
   Expand-Archive -Path C:\git\policyfiles\downloads\Server2019Baseline.zip -DestinationPath C:\git\policyfiles\downloads\
   ```

4. Validate the Server 2019 Baseline contents using **MapGuidsToGpoNames.ps1**:

   ```azurepowershell-interactive
   # Show content details 
   C:\git\policyfiles\downloads\Scripts\Tools\MapGuidsToGpoNames.ps1 -rootdir C:\git\policyfiles\downloads\GPOs\ -Verbose
   ```

## Convert from Group Policy to Guest Configuration Policy

Next, we convert the downloaded Server 2019 Baseline into a Guest Configuration Package using the Guest Configuration and Baseline Management modules. 

1. Convert the Group Policy to Desired State Configuration using the Baseline Management Module:

   ```azurepowershell-interactive
   ConvertFrom-GPO -Path 'C:\git\policyfiles\downloads\GPOs\{3657C7A2-3FF3-4C21-9439-8FDF549F1D68}\' -OutputPath 'C:\git\policyfiles\' -OutputConfigurationScript -Verbose
   ```

2. Rename and reformat the converted scripts to create a policy content package:

   ```azurepowershell-interactive
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('DSCFromGPO', 'Server2019Baseline') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('PSDesiredStateConfiguration', 'PSDscResources') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   C:\git\policyfiles\Server2019Baseline.ps1
   ```

3. Create a Guest Configuration policy content package:

   ```azurepowershell-interactive
   New-GuestConfigurationPackage -Name Server2019Baseline -Configuration c:\git\policyfiles\localhost.mof -DestinationPath C:\git\policyfiles\ -Verbose
   ```

## Publish Guest Configuration Policy

The next step is to publish the file to blob storage. 

1. The script below contains a function you can use to automate this task. Note, the commands used in the `publish` function require the `Az.Storage` module.

   ```azurepowershell-interactive
    function publish {
        param(
        [Parameter(Mandatory=$true)]
        $resourceGroup,
        [Parameter(Mandatory=$true)]
        $storageAccountName,
        [Parameter(Mandatory=$true)]
        $storageContainerName,
        [Parameter(Mandatory=$true)]
        $filePath,
        [Parameter(Mandatory=$true)]
        $blobName
        )

        # Get Storage Context
        $Context = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
            -Name $storageAccountName | `
            ForEach-Object { $_.Context }

        # Upload file
        $Blob = Set-AzStorageBlobContent -Context $Context `
            -Container $storageContainerName `
            -File $filePath `
            -Blob $blobName `
            -Force

        # Get url with SAS token
        $StartTime = (Get-Date)
        $ExpiryTime = $StartTime.AddYears('3')  # THREE YEAR EXPIRATION
        $SAS = New-AzStorageBlobSASToken -Context $Context `
            -Container $storageContainerName `
            -Blob $blobName `
            -StartTime $StartTime `
            -ExpiryTime $ExpiryTime `
            -Permission rl `
            -FullUri

        # Output
        return $SAS
    }
   ```

2. Create parameters to define the unique resource group, storage account, and container. 
   
   ```azurepowershell-interactive
    # Replace the $resourceGroup, $storageAccount, and $storageContainer values below.
    $resourceGroup = 'rfc_customguestconfig'
    $storageAccount = 'guestconfiguration'
    $storageContainer = 'content'
    $path = 'c:\git\policyfiles\Server2019Baseline\Server2019Baseline.zip'
    $blob = 'Server2019Baseline.zip' 
    ```

3. Use the publish function with the assigned parameters to publish the Guest Configuration package to public blob storage.

   ```azurepowershell-interactive
    $uri = publish `
      -resourceGroup $resourceGroup `
      -storageAccountName $storageAccount `
      -storageContainerName $storageContainer `
      -filePath $path `
      -blobName $blob
    ```

4. Once a Guest Configuration custom policy package has been created and uploaded, create the Guest Configuration policy definition. To create the Guest Configuration, we use the `New-GuestConfigurationPolicy` cmdlet:

	```azurepowershell-interactive
	New-GuestConfigurationPolicy `
		-ContentUri $Uri 
	    -DisplayName 'Server 2019 Configuration Baseline'
	    -Description 'Validation of using a completely custom baseline configuration for Windows VMs' 
	    -Version 1.0.0.0 
	    -DestinationPath C:\git\policyfiles\policy 
	    -Platform Windows 
	    -Verbose
	```
	
5. Publish the policy definitions using the `Publish-GuestConfigurationPolicy` cmdlet. The cmdlet only has the **Path** parameter that points to the location of the JSON files created by `New-GuestConfigurationPolicy`. To run the Publish command, you need access to create policies in Azure. The specific authorization requirements are documented in the [Azure Policy Overview](../overview.md) page. The best built-in role is **Resource Policy Contributor**.

	```azurepowershell-interactive
	Publish-GuestConfigurationPolicy -Path C:\git\policyfiles\policy\ -Verbose
	```markdown

## Assign Guest Configuration Policy

With the policy created in Azure, the last step is to assign the initiative. See how to assign the
initiative with [Portal](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md), and
[Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> Guest Configuration policies must **always** be assigned using the initiative that combines the
> _AuditIfNotExists_ and _DeployIfNotExists_ policies. If only the _AuditIfNotExists_ policy is
> assigned, the prerequisites aren't deployed and the policy always shows that '0' servers are
> compliant.

Assigning a policy definition with _DeployIfNotExists_ effect requires an additional level of
access. To grant the least privilege, you can create a custom role definition that extends
**Resource Policy Contributor**. The example below creates a role named **Resource Policy
Contributor DINE** with the additional permission _Microsoft.Authorization/roleAssignments/write_.

```azurepowershell-interactive
$subscriptionid = '00000000-0000-0000-0000-000000000000'
$role = Get-AzRoleDefinition "Resource Policy Contributor"
$role.Id = $null
$role.Name = "Resource Policy Contributor DINE"
$role.Description = "Can assign Policies that require remediation."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/$subscriptionid")
New-AzRoleDefinition -Role $role
```

## Next steps

- Learn about auditing VMs with [Guest Configuration](../concepts/guest-configuration.md).
- Understand how to [programmatically create policies](programmatically-create.md).
- Learn how to [get compliance data](get-compliance-data.md).