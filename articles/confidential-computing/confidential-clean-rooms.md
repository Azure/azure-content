---
title: Perform secure multiparty data collaboration on Azure
description: Learn how Azure Confidential Clean Rooms enables multiparty collaborations while keeping your data safe from other collaborators.
author: mathapli
ms.service: azure
ms.subservice: confidential-computing
ms.topic: conceptual
ms.date: 10/28/2024
ms.author: mathapli
---

# Azure Confidential Clean Rooms

> [!NOTE]
> Azure Confidential Clean Rooms is currently in Gated Preview. Please fill the form at https://aka.ms/ACCRPreview and we will reach out to you with next steps.

Azure Confidential Clean Rooms, aka ACCR, offers a secure and compliant environment that helps organizations overcome the challenges of using privacy-sensitive data for data analytics, AI model development and inferencing. Built on top of [Confidential containers or C-ACI](../confidential-computing/confidential-containers.md), the ACCR secures the data and the model from exfiltration outside the clean room boundary.
Organizations can safely collaborate and analyze sensitive data within the sandbox, without violating compliance standards or risking data breaches by using advanced privacy-enhancing technologies like secure governance & audit, secure collaboration (TEE), verifiable trust, differential privacy, and controlled access.

## Who should use Azure Confidential Clean Rooms?
Azure Confidential Clean Rooms could be a great choice for you if you have these scenarios: 

- Data analytics and inferencing: Organizations looking to build insights on second-party data while ensuring data privacy can use ACCR. ACCR is useful when data providers are concerned about data exfiltration. ACCR ensures that data is only used for agreed purposes and safeguards against unauthorized access or egress (as it's a sandboxed environment). 
- Data privacy ISVs: Independent Software Vendors (ISVs) who provide secure multiparty data collaboration services can use ACCR as an extensible platform. It allows them to add enforceable tamperproof contracts with governance and audit capabilities, and uses [Confidential containers or C-ACI](../confidential-computing/confidential-containers.md) underneath to ensure data is encrypted during processing so that their customers' data remains secure.
- ML fine tuning: ACCR provides a solution to organizations that require data from various sources to train or fine-tune machine learning models but face data sharing regulations. It allows any party to audit and confirm that data is being used only for the agreed purpose, such as ML modeling.
- ML inferencing: Organizations can use ACCR in machine learning (ML) inferencing to enable secure, collaborative data analysis without compromising privacy or data ownership. ACCR acts as secure environment where multiple parties can combine sensitive data and apply ML models for inferencing while keeping raw data inaccessible to others.

### Industries that can successfully utilize ACCR
- Healthcare- In the healthcare industry, Azure Confidential Clean Rooms enable secure collaboration on sensitive patient data. For example, healthcare providers can use clean rooms to train and fine-tune AI/ML models for predictive diagnostics, personalized medicine, and clinical decision support. By using confidential computing, healthcare organizations can protect patient privacy while collaborating with other institutions to improve healthcare outcomes.
ACCR can also be used for ML inferencing where partner hospitals can utilize power of these models for early detection.
- Advertising- In the advertising industry, Azure Confidential Clean Rooms facilitates secure data sharing between advertisers and publishers. ACCR enables targeted advertising and campaign effectiveness measurement without exposing sensitive user data.
- Banking, Financial Services and Insurance (BFSI) - The BFSI sector can use Azure Confidential Clean Rooms to securely collaborate on financial data, ensuring compliance with regulatory requirements. This enables financial institutions to perform joint data analysis and develop risk models, fraud detection models, lending scenarios among others without exposing sensitive customer information.
- Retail- In the retail industry, Azure Confidential Clean Rooms enables secure collaboration on customer data to enhance personalized marketing and inventory management. Retailers can use clean rooms to analyze customer behavior and preferences to create personalized marketing campaigns without compromising data privacy.

## Benefits

:::image type="content" source="./media/confidential-clean-rooms/accr-benefits.png" alt-text="Diagram of Azure Confidential Clean Rooms benefits, showing zero trust, no data duplication of container workloads, and managed governance.":::

Azure Confidential Clean Rooms (ACCR) provides a secure and compliant environment for multi-party data collaboration. Built on [Confidential containers or C-ACI](../confidential-computing/confidential-containers.md), ACCR ensures that sensitive data remains protected throughout the collaboration process. Here are some key benefits of using Azure Confidential Clean Rooms:

- Secure collaboration and governance:
ACCR allows collaborators to create tamper-proof contracts. ACCR also enforces all the constraints which are part of the contract. Governance ensures validity of constraints before allowing data to be released into clean rooms and drives transparency among collaborators by generating tamper-proof audit trails. ACCR uses the open-sourced [confidential consortium framework](https://microsoft.github.io/CCF/main/overview/what_is_ccf.html) to enable these capabilities.
- Compliance:
Confidential computing can address some of the regulatory and privacy concerns by providing a secure environment for data collaboration. This capability is beneficial for industries such as financial services, healthcare, and telecom, which deal with highly sensitive data and personally identifiable information (PII).
- Enhanced data security:
ACCR is built using confidential computing to provide a hardware-based, trusted execution environment (TEE). This environment is sandboxed and allows only authorized workloads to execute and prevents unauthorized access to data or code during processing, ensuring that sensitive information remains secure.
- Verifiable trust at each step with the help of cryptographic remote attestation forms the cornerstone of Azure Confidential Clean Rooms.

- Cost-effective: 
By providing a secure and compliant environment for data collaboration, ACCR reduces the need for costly and complex data protection measures. This makes it a cost-effective solution for organizations looking to use sensitive data for analysis and insights.

:::image type="content" source="./media/confidential-clean-rooms/accr-illustration.png" alt-text="Diagram of Azure Confidential Clean Rooms benefits, showing all steps of clean room creation.":::


## Onboarding to Azure Confidential Clean Rooms
ACCR is currently in Gated Preview. To express your interest in joining the gated preview, follow these steps:
- Fill and submit the form at https://aka.ms/ACCR-Preview-Onboarding.
- Once you submit, further steps will be shared with you on onboarding. 
- For further questions on onboarding reach out to  CleanRoomPMTeam@microsoft.com.
- After reviewing details, we'll reach out to you with detailed steps for onboarding.

## Important References

- [CleanRoom Getting started samples](https://github.com/Azure-Samples/azure-cleanroom-samples)
- [Confidential Clean Room sidecars repository](https://github.com/Azure/azure-cleanroom/)

## Frequently asked questions

- Question: Where are the sidecars container images of Clean Room published at ?
  Answer: The sidecar container images are published at mcr.microsoft.com/cleanroom.

- Question: Can more than two collaborators participate in a collaboration?
  Answer: Yes, more than two collaborators can become part of collaboration. This allows multiple data providers to share data in the clean room.

If you have questions about Azure Confidential Clean Rooms, reach out to <accrsupport@microsoft.com>.

## Next steps

- [Deploy Confidential container group with Azure Container Instances](/azure/container-instances/container-instances-tutorial-deploy-confidential-containers-cce-arm)
- [Microsoft Azure Attestation](/azure/attestation/overview)