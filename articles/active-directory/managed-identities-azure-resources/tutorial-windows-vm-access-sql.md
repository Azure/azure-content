---
title: Tutorial`:` Use a managed identity to access Azure SQL Database - Windows - Azure AD
description: A tutorial that walks you through the process of using a Windows VM system-assigned managed identity to access Azure SQL Database.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba

ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: markvi
ms.collection: M365-identity-device-management
---
# Tutorial: Use a Windows VM system-assigned managed identity to access Azure SQL

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

This tutorial shows you how to use a system-assigned identity for a Windows virtual machine (VM) to access Azure SQL Database. Managed Service Identities are automatically managed by Azure and enable you to authenticate to services that support Azure AD authentication, without needing to insert credentials into your code. You learn how to:

> [!div class="checklist"]
>
> * Grant your VM access to Azure SQL Database
> * Enable Azure AD authentication
> * Create a contained user in the database that represents the VM's system assigned identity
> * Get an access token using the VM identity and use it to query Azure SQL Database

## Prerequisites

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## Enable

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]

## Grant access

To grant your VM access to a database in Azure SQL Database, you can use an existing [logical SQL server](../../azure-sql/database/logical-servers.md) or create a new one. To create a new server and database using the Azure portal, follow this [Azure SQL quickstart](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal). There are also quickstarts that use the Azure CLI and Azure PowerShell in the [Azure SQL documentation](https://docs.microsoft.com/azure/sql-database/).

There are two steps to granting your VM access to a database:

1. Enable Azure AD authentication for the server.
2. Create a **contained user** in the database that represents the VM's system-assigned identity.

### Enable Azure AD authentication

**To [configure Azure AD authentication](/azure/sql-database/sql-database-aad-authentication-configure):**

1. In the Azure portal, select **SQL servers** from the left-hand navigation.
2. Click the SQL server to be enabled for Azure AD authentication.
3. In the **Settings** section of the blade, click **Active Directory admin**.
4. In the command bar, click **Set admin**.
5. Select an Azure AD user account to be made an administrator of the server, and click **Select.**
6. In the command bar, click **Save.**

### Create contained user

This section shows how to create a contained user in the database that represents the VM's system assigned identity. For this step, you need [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Before beginning, it may also be helpful to review the following articles for background on Azure AD integration:

* [Universal Authentication with SQL Database and SQL Data Warehouse (SSMS support for MFA)](/azure/sql-database/sql-database-ssms-mfa-authentication)
* [Configure and manage Azure Active Directory authentication with SQL Database or SQL Data Warehouse](/azure/sql-database/sql-database-aad-authentication-configure)

SQL Database requires unique AAD display names. With this, the AAD accounts such as users, groups and Service Principals (applications) and VM names enabled for managed identity must be uniquely defined in AAD regarding their display names. SQL Database checks the AAD display name during T-SQL creation of such users and if it is not unique, the command fails requesting to provide a unique AAD display name for a given account.

**To create a contained user:**

1. Start SQL Server Management Studio.
2. In the **Connect to Server** dialog, Enter your server name in the **Server name** field.
3. In the **Authentication** field, select **Active Directory - Universal with MFA support**.
4. In the **User name** field, enter the name of the Azure AD account that you set as the server administrator, for example, helen@woodgroveonline.com
5. Click **Options**.
6. In the **Connect to database** field, enter the name of the non-system database you want to configure.
7. Click **Connect**. Complete the sign-in process.
8. In the **Object Explorer**, expand the **Databases** folder.
9. Right-click on a user database and click **New query**.
10. In the query window, enter the following line, and click **Execute** in the toolbar:

    > [!NOTE]
    > `VMName` in the following command is the name of the VM that you enabled system assigned identity on in the prerequsites section.

    ```sql
    CREATE USER [VMName] FROM EXTERNAL PROVIDER
    ```

    The command should complete successfully, creating the contained user for the VM's system-assigned identity.
11. Clear the query window, enter the following line, and click **Execute** in the toolbar:

    > [!NOTE]
    > `VMName` in the following command is the name of the VM that you enabled system assigned identity on in the prerequsites section.

    ```sql
    ALTER ROLE db_datareader ADD MEMBER [VMName]
    ```

    The command should complete successfully, granting the contained user the ability to read the entire database.

Code running in the VM can now get a token using its system-assigned managed identity and use the token to authenticate to the server.

## Access data

This section shows how to get an access token using the VM's system-assigned managed identity and use it to call Azure SQL. Azure SQL natively supports Azure AD authentication, so it can directly accept access tokens obtained using managed identities for Azure resources. You use the **access token** method of creating a connection to SQL. This is part of Azure SQL's integration with Azure AD, and is different from supplying credentials on the connection string.

Test the end to end setup without having to write and deploy an app on the VM by using PowerShell.

1. In the portal, navigate to **Virtual Machines** and go to your Windows virtual machine and in the **Overview**, click **Connect**.
2. Enter in your **Username** and **Password** for which you added when you created the Windows VM.
3. Now that you have created a **Remote Desktop Connection** with the virtual machine, open **PowerShell** in the remote session.
4. Using PowerShell’s `Invoke-WebRequest`, make a request to the local managed identity's endpoint to get an access token for Azure SQL.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatabase.windows.net%2F' -Method GET -Headers @{Metadata="true"}
    ```

    Convert the response from a JSON object to a PowerShell object.

    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Extract the access token from the response.

    ```powershell
    $AccessToken = $content.access_token
    ```

5. Open a connection to the server. Remember to replace the values for AZURE-SQL-SERVERNAME and DATABASE.

    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Next, create and send a query to the server. Remember to replace the value for TABLE.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Examine the value of `$DataSet.Tables[0]` to view the results of the query.

## Modify your project

The steps you follow for your project depends on whether it's an ASP.NET project or an ASP.NET Core project.

- [Modify ASP.NET](#modify-aspnet)
- [Modify ASP.NET Core](#modify-aspnet-core)

> [!NOTE]
> The steps covered in this tutorial support the following versions:
> 
> - .NET Framework 4.7.2 and above
> - .NET Core 2.2 and above
>

### Modify ASP.NET

In Visual Studio, open the Package Manager Console and add the NuGet package [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

In *Web.config*, working from the top of the file and make the following changes:

- In `<configSections>`, add the following section declaration in it:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- below the closing `</configSections>` tag, add the following XML code for `<SqlAuthenticationProviders>`.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Find the connection string and replace its `connectionString` value with `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Replace _\<server-name>_ and _\<db-name>_ with your server name and database name.

> [!NOTE]
> The SqlAuthenticationProvider you just registered is based on top of the AppAuthentication library you installed earlier. By default, it uses a system-assigned identity. To leverage a user-assigned identity, you will need to provide an additional configuration. Please see [connection string support](../key-vault/general/service-to-service-authentication.md#connection-string-support) for the AppAuthentication library.

That's every thing you need to connect to SQL Database. When debugging in Visual Studio, your code uses the Azure AD user you configured in [Set up Visual Studio](#set-up-visual-studio). You'll set up SQL Database later to allow connection from the managed identity of your App Service app.

### Modify ASP.NET Core

In Visual Studio, open the Package Manager Console and add the NuGet package [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

In *appsettings.json*, replace the connection string value with:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

Next, you supply the Entity Framework database context with the access token for the SQL Database. In *Data\MyDatabaseContext.cs*, add the following code inside the curly braces of the empty `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` constructor:

```csharp
var conn = (Microsoft.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!NOTE]
> This demonstration code is synchronous for clarity and simplicity.

That's every thing you need to connect to SQL Database. When debugging in Visual Studio, your code uses the Azure AD user you configured in [Enable Azure AD authentication](#enable-azure-ad-authentication). The `AzureServiceTokenProvider` class caches the token in memory and retrieves it from Azure AD just before expiration. You don't need any custom code to refresh the token.

> [!TIP]
> If the Azure AD user you configured has access to multiple tenants, call `GetAccessTokenAsync("https://database.windows.net/", tenantid)` with the desired tenant ID to retrieve the proper access token.

## Disable

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]

## Next steps

In this tutorial, you learned how to use a system-assigned managed identity to access Azure SQL Database. To learn more about Azure SQL Database see:

> [!div class="nextstepaction"]
> [Azure SQL Database](/azure/sql-database/sql-database-technical-overview)
