---
title: SAML App Multi-Instancing
description: Learn about SAML App multi-instancing, which refers to the need for the configuration of multiple instances of the same application within a tenant. 
services: active-directory
author: swngdncer1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: reference
ms.date: 06/22/2022
ms.author: swngdncer1
ms.reviewer: paulgarn, ludwignick, jeedes, luleon
---

# SAML App Multi-Instancing

## What is App Multi-Instancing? 
App multi-instancing refers to the need for the configuration of multiple instances of the same application within a tenant.  For example, the organization has multiple Amazon Web Services accounts, each of which needs a separate service principal to handle instance-specific claims mapping (adding the AccountID claim for that AWS tenant) and roles assignment.  Or the customer has multiple instances of Box, which doesn’t need special claims mapping rather it needs separate service principals for separate signing keys.  

## IDP versus SP initiated SSO? 

A user can log in to an application one of two ways, either through the application itself, which is known as SP (service provider) initiated SSO, or by going directly to the identity provider, known as IDP initiated SSO. Depending on which approach is used within your organization you'll need to follow the appropriate instructions below.  

## SP Initiated   

The issue here lies with the fact that within the Saml request the Issuer specified is usually the App ID Uri. This doesn’t allow the customer to distinguish which instance of an application is being targeted when using Service Provider initiated SSO.   

## Configuration Instructions  

Update the Saml single sign-on service URL configured within the service provider for each instance to include the service principal guid as part of the URL. For example, the general SSO login URL for Saml would have been `https://login.microsoftonline.com/<tenantid>/saml2`, this can now be updated to target a specific service principal as follows `https://login.microsoftonline.com/<tenantid>/saml2/<issuer>`.  

Only service principal id's in GUID format will be accepted for the ‘issuer’ value. The service principal id will override the issuer in the SAML request/response, and the rest of the flow will be executed as usual. There's one exception: if the application requires the request to be signed, the request will be rejected even if the signature was valid. This is done to avoid any security risks with functionally overriding values in a signed request.  

## IDP Initiated   

IDP Initiated feature exposes two settings for each application.   

- An “audience override” option exposed for configuration via claims mapping or the portal.  The intended use case are applications that require the same audience for multiple instances. This setting will be ignored if no custom signing key is configured for the application.    

- An “issuer with application id” flag to indicate the issuer should be unique for each application instead of unique for each tenant.  This setting will be ignored if no custom signing key is configured for the application.  

## Configuration Instructions  

Graph – see [Claims mapping policy](reference-claims-mapping-policy-type.md) 
Portal – see [Customize app SAML token claims](active-directory-saml-claims-customization.md)

## Next steps

- To learn how to customize the claims emitted in tokens for a specific application in their tenant using PowerShell, see [How to: Customize claims emitted in tokens for a specific app in a tenant](active-directory-claims-mapping.md)
- To learn how to customize claims issued in the SAML token through the Azure portal, see [How to: Customize claims issued in the SAML token for enterprise applications](active-directory-saml-claims-customization.md)
- To learn more about extension attributes, see [Using directory schema extension attributes in claims](active-directory-schema-extensions.md).
