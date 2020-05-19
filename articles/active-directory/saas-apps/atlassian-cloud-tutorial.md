---
title: 'Tutorial: Azure Active Directory integration with Atlassian Cloud | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Atlassian Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: celested

ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: jeedes

ms.collection: M365-identity-device-management
---
# Tutorial: Integrate Atlassian Cloud with Azure Active Directory

In this tutorial, you'll learn how to integrate Atlassian Cloud with Azure Active Directory (Azure AD). When you integrate Atlassian Cloud with Azure AD, you can:

* Control in Azure AD who has access to Atlassian Cloud.
* Enable your users to be automatically signed-in to Atlassian Cloud with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get one-month free trial [here](https://azure.microsoft.com/pricing/free-trial/).
* Atlassian Cloud single sign-on (SSO) enabled subscription.
* To enable Security Assertion Markup Language (SAML) single sign-on for Atlassian Cloud products, you need to set up Atlassian Access. Learn more about [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager).

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment. 

* Atlassian Cloud supports **SP and IDP** initiated SSO
* Atlassian Cloud supports [Automatic user provisioning and deprovisioning](atlassian-cloud-provisioning-tutorial.md)
* Once you configure Atlassian Cloud you can enforce session control, which protect exfiltration and infiltration of your organization’s sensitive data in real-time. Session control extend from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
## Adding Atlassian Cloud from the gallery

To configure the integration of Atlassian Cloud into Azure AD, you need to add Atlassian Cloud from the gallery to your list of managed SaaS apps.

1. Sign in to the [Azure portal](https://portal.azure.com) using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Atlassian Cloud** in the search box.
1. Select **Atlassian Cloud** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD single sign-on

Configure and test Azure AD SSO with Atlassian Cloud using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Atlassian Cloud.

To configure and test Azure AD SSO with Atlassian Cloud, complete the following building blocks:

1. **[Configure Azure AD with Atlassian Cloud SSO](#configure-azure-ad-sso)** - to enable your users to use Azure AD based SAML SSO with Atlassian Cloud.
    * **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    * **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Create Atlassian Cloud test user](#create-atlassian-cloud-test-user)** - to have a counterpart of B.Simon in Atlassian Cloud that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

### Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. Before you start go to your Atlassian product instance and copy/save the Instance URL
   > [!NOTE]
   > url should fit `https://<instancename>.atlassian.net` pattern
   ![image](common/get_atlassian_instance_name.png)
1. Open the [Atlassian Admin Portal](https://admin.atlassian.com/) and click on your organization name
   ![image](common/click_on_organization_in_atlassian_access.png)
1. You need to verify your domain before going to configure single sign-on. For more information, see [Atlassian domain verification](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) document.
1. From the Atlassian Admin Portal Screen select **Security** from the left drawer
   ![image](common/click_on_security_in_atlassian_access.png)
1. From the Atlassian Admin Portal Security Screen select **SAML single sign** on from the left drawer
   ![image](common/click_on_saml_sso_in_atlassian_access_security.png)
1. Click on **Add SAML Configuration** and keep the page open
   ![image](common/click_on_add_saml_configuration_in_atlassian_access_security_saml_sso.png)
   ![image](common/add_saml_configuration_in_atlassian_access_security_saml_sso.png)
1. In the [Azure portal](https://portal.azure.com/), on the **Atlassian Cloud** application integration page, find the **Manage** section and select **Set up single sign-on**.
   ![image](common/click_on_set_up_sso_in_azure_atlassian_cloud_getting_started.png)
1. On the **Select a Single sign-on method** page, select **SAML**.
   ![image](common/click_on_saml_in_azure_atlassian_cloud_select_sso_method.png)
1. On the **Set up Single Sign-On with SAML** page, scroll down to **Set Up Atlassian Cloud**
   
   a. Click on **Configuration URLs**
   ![image](common/click_on_configuration_urls_in_azure_atlassian_cloud_set_up_atlassian_cloud.png)
   
   b. Copy **Azure AD Identifier** from Azure → **Identity Provider Entity ID** in Atlassian
   
   c. Copy **Login URL** from Azure → **Identity Provider SSO URL** in Atlassian
   ![image](common/copy_configuration_urls_in_azure_atlassian_cloud_set_up_atlassian_cloud.png)
   ![image](common/set_entity_id_and_sso_url_in_add_saml_configuration_in_atlassian_access_security_saml_sso.png)

1. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.
   ![image](common/download_public_x509_certificate_in_azure_atlassian_cloud_set_up_atlassian_cloud.png)
   ![image](common/paste_public_x509_certificate_in_add_saml_configuration_in_atlassian_access_security_saml_sso.png)

1. **Add/Save** the SAML Configuration in Atlassian
1. If you wish to configure the application in **IDP** initiated mode, edit the **Basic SAML Configuration** section of the **Set up Single Sign-On with SAML** page in Azure copy and open the **SAML single sign-on page** on the Atlassian Admin Portal

   a. Copy **SP Entity ID** from Atlassian → **Identifier (Entity ID)** in Azure and set it as default
   
   b. Copy **SP Assertion Consumer Service URL** from Atlassian → **Reply URL (Assertion Consumer Service URL)** in Azure  and set it as default
   
   c. Copy your **Instance URL** (from step 1)  → **Relay State** in Azure
   ![image](common/copy_entity_ID_assertion_consumer_service_url_in_atlassian_access_security_saml_sso.png)
   ![image](common/click_on_edit_button_in_azure_atlassian_cloud_set_up_atlassian_cloud_basic_saml_configuration.png)
   ![image](common/set_entity_ID_reply_URL_relay_state_in_azure_atlassian_cloud_set_up_atlassian_cloud_basic_saml_configuration.png)
   
1. If you wish to configure the application in **SP** initiated mode, edit the **Basic SAML Configuration** section of the **Set up Single Sign-On with SAML** page in Azure. Copy your **Instance URL** (from step 1)  → **Sign On URL** in Azure
   ![image](common/click_on_edit_button_in_azure_atlassian_cloud_set_up_atlassian_cloud_basic_saml_configuration.png)
   ![image](common/set_sign_on_URL_in_azure_atlassian_cloud_set_up_atlassian_cloud_basic_saml_configuration.png)
   
1. Your Atlassian Cloud application expects the SAML assertions in a specific format, which requires you to add custom attribute mappings to your SAML token attributes configuration.

   a. **Edit** the attribute mappings
   ![image](common/click_on_edit_button_in_azure_atlassian_cloud_set_up_atlassian_cloud_user_attributes_and_claims.png)
   
   b. Attribute mapping for an Azure AD tenant with an Office 365 license
      
      i. Click on the **Unique User Identifier (Name ID)** claim
      ![image](common/click_on_unique_user_identifier_in_azure_atlassian_cloud_set_up_atlassian_cloud_user_attributes_and_claims.png)
      
      ii. Atlassian Cloud expects the *nameidentifier* (**Unique User Identifier**) to be mapped to the user’s email (**user.email**). Edit the **Source attribute** and change it to **user.mail**. Save the changes to the claim.
      ![image](common/set_unique_user_identifier_in_azure_atlassian_cloud_set_up_atlassian_cloud_user_attributes_and_claims.png)
      
      iii. The final attribute mappings should look as follows.
      ![image](common/final_user_attributes_and_claims_for_azure_tenant_with_O365_in_azure_atlassian_cloud_set_up_atlassian_cloud_user_attributes_and_claims.png)
      
   c. Attribute mapping for an Azure AD tenant without an Office 365 license 

      i. Click on the **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** claim.
      ![image](common/click_on_email_address_in_azure_atlassian_cloud_set_up_atlassian_cloud_user_attributes_and_claims.png)
         
      ii. While Azure does not populate the **user.mail** attribute for users created in Azure AD tenants without Office 365 licenses and stores the email for such users in **userprincipalname** attribute. Atlassian Cloud expects the *nameidentifier* (**Unique User Identifier**) to be mapped to the user’s email (**user.userprincipalname**).  Edit the **Source attribute**  and change it to **user.userprincipalname**. Save the changes to the claim.
      ![image](common/set_email_address_in_azure_atlassian_cloud_set_up_atlassian_cloud_user_attributes_and_claims.png)
         
      iii. The final attribute mappings should look as follows.
      ![image](common/final_user_attributes_and_claims_for_azure_tenant_without_O365_in_azure_atlassian_cloud_set_up_atlassian_cloud_user_attributes_and_claims.png)
     
### Create an Azure AD test user

In this section, you'll create a test user in the Azure portal called B.Simon.

1. From the left pane in the Azure portal, select **Azure Active Directory**, select **Users**, and then select **All users**.
1. Select **New user** at the top of the screen.
1. In the **User** properties, follow these steps:
   1. In the **Name** field, enter `B.Simon`.  
   1. In the **User name** field, enter the username@companydomain.extension. For example, `B.Simon@contoso.com`.
   1. Select the **Show password** check box, and then write down the value that's displayed in the **Password** box.
   1. Click **Create**.

### Assign the Azure AD test user

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Atlassian Cloud.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Atlassian Cloud**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![The "Users and groups" link](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

	![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. In the **Add Assignment** dialog, click the **Assign** button.

### Create Atlassian Cloud test user

To enable Azure AD users to sign in to Atlassian Cloud, provision the user accounts manually in Atlassian Cloud by doing the following:

1. In the **Administration** pane, select **Users**.

	![The Atlassian Cloud Users link](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. To create a user in Atlassian Cloud, select **Invite user**.

	![Create an Atlassian Cloud user](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. In the **Email address** box, enter the user's email address, and then assign the application access.

	![Create an Atlassian Cloud user](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. To send an email invitation to the user, select **Invite users**. An email invitation is sent to the user and, after accepting the invitation, the user is active in the system.

> [!NOTE]
> You can also bulk-create users by selecting the **Bulk Create** button in the **Users** section.

### Test SSO

When you select the Atlassian Cloud tile in the Access Panel, you should be automatically signed in to the Atlassian Cloud for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## Additional Resources

- [List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [What is conditional access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Try Atlassian Cloud with Azure AD](https://aad.portal.azure.com/)

- [What is session control in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [How to protect Atlassian Cloud with advanced visibility and controls](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)