---
title: Enrich a Cognitive Search index with custom classes
titleSuffix: Azure Cognitive Services
description: Improve your cognitive search indices using custom cassifications
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: tutorial
ms.date: 01/21/2022
ms.author: aahi
ms.custom: 
---

# Tutorial: Enrich Cognitive search index with custom classifcations from your data

With the abundance of electronic documents within the enterprise, the problem of search through them becomes a tiring and expensive task. [Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-create-service-portal) helps with searching through your files based on their indices. Custom classification helps in enriching the indexing of these files by classifying them into your custom classes.

In this tutorial, you learn how to:

* Create a custom classification project.
* Publish Azure function.
* Add Index to your Azure Cognitive search.

## Prerequisites

* [An Azure Language resource connected to an Azure blob storage account](../how-to/create-project.md).
    * We recommend following the instructions for creating a resource using the Azure portal, for easier setup. 

* [An Azure Cognitive Search service](../../../../search/search-create-service-portal.md) in your current subscription
    * You can use any tier, and any region for this service.

* An [Azure function app](../../../../azure-functions/functions-create-function-app-portal.md)

* Download this [sample data](). <!-- TODO: add link to sample data here (Movies)-->

## Create a custom classification project through Language studio

1. Log in to [Language Studio](https://aka.ms/languageStudio). A window will appear to let you select your subscription and Language resource. Select the resource you created in the above step.

2. Under the **Classify text** section of Language Studio, select **custom text classification** from the available services, and select it.
       
3. Select **Create new project** from the top menu in your projects page. Creating a project will let you tag data, train, evaluate, improve, and deploy your models. 

4. If you have created your resource using the steps above in this [guide](..//how-to/create-project.md#azure-resources), the **Connect storage** step will be completed already. If not, you need to assign [roles for your storage account](../../how-to/create-project.md#roles-for-your-storage-account) before connecting it to your resource.

5. Select your project type. For this quickstart, we will create a multi label classification project where you can assign multiple classes to the same file. Then click **Next**. Learn more about [project types](../../glossary.md#project-types)

6. Enter project information, including a name, description, and the language of the files in your project. You will not be able to change the name of your project later.
    >[!TIP]
    > Your dataset doesn't have to be entirely in the same language. You can have multiple files, each with different supported languages. If your dataset contains files of different languages or if you expect different languages during runtime, select **enable multi-lingual dataset** when you enter the basic information for your project.

7. Select the container where you have uploaded your data. For this tutorial we will use the tags file you downloaded from the sample data.

8. Review the data you entered and select **Create Project**.

## Train your model

1. Select **Train** from the left side menu.

2. To train a new model, select **Train a new model** and type in the model name in the text box below.

    :::image type="content" source="../media/train-model.png" alt-text="Create a new model" lightbox="../media/train-model.png":::

3. Select the **Train** button at the bottom of the page.

After training is completed, you can [view the model's evaluation details](../how-to/view-model-evaluation.md) and [improve the model](../how-to/improve-model.md)

## Deploy your model

1. Select **Deploy model** from the left side menu.

2. Select the model you want to deploy and from the top menu click on **Deploy model**. If you deploy your model through Langugae Studio, you `deployment-name` will be `prod`.

## Use CogSvc language utilties tool for Cognitive search integration
 
### Publish your Azure Function

1. Download and use the [provided sample function](https://aka.ms/CustomTextAzureFunction).

2. After you download the sample function, open the *program.cs* file in Visual Studio and [publish the function to Azure](../../../../azure-functions/functions-develop-vs.md?tabs=in-process#publish-to-azure).

### Prepare configuration file

1. Download [sample configuration file](https://aka.ms/CognitiveSearchIntegrationToolAssets) and open it in a text editor. 

2. Get your storage account connection string by:
    
    1. Navigating to your storage account overview page in the [Azure portal](https://ms.portal.azure.com/#home). 
    2. In the **Access Keys** section in the menu to the left of the screen, copy your **Connection string** to the `connectionString` field in the configuration file, under `blobStorage`.
    3. Go to the container where you have the files you want to index and copy container name to the `containerName` field in the configuration file, under `blobStorage`. 

3. Get your cognitive search endpoint and keys by:
    
    1. Navigating to your resource overview page in the [Azure portal](https://ms.portal.azure.com/#home).
    2. Copy the **Url** at the top-right section of the page to the `endpointUrl` field within `cognitiveSearch`.
    3. Go to the **Keys** section in the menu to the left of the screen. Copy your **Primary admin key** to the `apiKey` field within `cognitiveSearch`.

4. Get Azure Function endpoint and keys
   
    1. To get your Azure Function endpoint and keys, go to your function overview page in the [Azure portal](https://ms.portal.azure.com/#home).
    2. Go to **Functions** menu on the left of the screen, and click on the function you created.
    3. From the top menu, click **Get Function Url**. The URL will be formatted like this: `YOUR-ENDPOINT-URL?code=YOUR-API-KEY`. 
    4. Copy `YOUR-ENDPOINT-URL` to the `endpointUrl` field in the configuration file, under `azureFunction`. 
    5. Copy `YOUR-API-KEY` to the `apiKey` field in the configuration file, under `azureFunction`. 

5. Get your resource keys endpoint

    1. Navigate to your resource in the [Azure portal](https://ms.portal.azure.com/#home).
    2. From the menu on the left side, select **Keys and Endpoint**. You will need the endpoint and one of the keys for the API requests.

        :::image type="content" source="../../media/azure-portal-resource-credentials.png" alt-text="A screenshot showing the key and endpoint screen in the Azure portal" lightbox="../../media/azure-portal-resource-credentials.png":::

6. Get your custom classification project secrets

    1. You will need your **project-name**, project names are case sensitive.

    2. You will also need the **deployment-name**. 
        * If you have deployed your model via Language Studio, your deployment name will be `prod` by default. 
        * If you have deployed your model programmatically, using the API, this is the deployment name you assigned in your request.

### Run the indexer command

After you have published your Azure function and prepared your configs file, you can run the indexer command.
```cli
    indexer index --index-name <name-your-index-here> --configs <absolute-path-to-configs-file>
```

Replace `name-your-index-here` with the index name that appears in your Cognitive Search instance.

## Next steps

* [Search your app with with the Cognitive Search SDK](../../../../search/search-howto-dotnet-sdk.md#run-queries)
