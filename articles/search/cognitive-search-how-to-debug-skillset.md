---
title: Debug a skillset
titleSuffix: Azure Cognitive Search
description: Investigate skillset errors and issues by starting a debug session in Azure portal.

manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.date: 12/31/2021
---

# Debug an Azure Cognitive Search skillset in Azure portal

Start a debug session to identify and resolve errors, validate changes, and push changes to a published skillset in your Azure Cognitive Search service.

A debug session is a cached indexer and skillset execution, scoped to a single document, that you can edit and test interactively. If you are unfamiliar with how a debug session works, see [Debug sessions in Azure Cognitive Search](cognitive-search-debug-session.md).

> [!Important]
> Debug sessions is a preview portal feature, provided under [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Prerequisites

+ An existing enrichment pipeline, including a data source, a skillset, an indexer, and an index.

+ Azure Storage, used to save session state.

Debug sessions work with all generally available data sources and most preview data sources. The MongoDB API (preview) of Cosmos DB is currently not supported.

## Create a debug session

1. [Sign in to Azure portal](https://portal.azure.com) and find your search service.

1. In the **Overview** page of your search service, select the **Debug Sessions** tab.

1. Select **+ New Debug Session**.

1. Provide a name for the session and specify a general-purpose storage account that will be used to cache the skill executions.

1. Select the indexer that drives the skillset you want to debug. Copies of both the indexer and skillset are used to create the session.

1. Choose a document. The session will default to the first document in the data source, but you can also choose which document to step through.

1. Optionally, specify any indexer execution settings that should be used to create the session. Any indexer options that you specify in a debug session have no effect on the indexer itself.

1. Select **Save Session** to get started.

   :::image type="content" source="media/cognitive-search-debug/debug-session-new.png" alt-text="Screenshot of a debug session page." border="true":::

The debug session begins by executing the indexer and skillset on the selected document. The document's content and metadata created will be visible and available in the session.

## Check field mappings

If you're debugging a skillset that isn't producing output, one of the first things to check is the field maps that specify how content moves out of the pipeline and into a search index.

1. Start with the default views: **AI enrichment > Skill Graph**, with the graph type set to **Dependency Graph**.

1. Select **Field Mappings** near the top. You should find at least the document key that uniquely identifies and associates each search document in the search index with it's source document in the data source. 

   If you are importing raw content straight from the data source, bypassing enrichment, you should find those fields in **Field Mappings**.

1. Select **Output Field Mappings** at the bottom of the graph. Here you will find mappings from skill outputs to target fields in the search index. Unless you used the Import Data wizard, output field mappings are defined manually and could be incomplete or mistyped. 

   Verify that the fields in **Output Field Mappings** exist in the search index as specified, checking for spelling and [enrichment node path syntax](cognitive-search-concept-annotations-syntax.md). 

   :::image type="content" source="media/cognitive-search-debug/output-field-mappings.png" alt-text="Screenshot of the Output Field Mappings node and details." border="true":::

## Check skills

If the field mappings are correct, check individual skills for configuration and content. If a skill fails to produce output, it might be missing a property or parameter, which can be determined through error and validation messages. 

Other issues, such as an invalid context or input expression, can be harder to resolve because the error will tell you what is wrong, but not how to fix it. For help with context and input syntax, see [Reference annotations in an Azure Cognitive Search skillset](cognitive-search-concept-annotations-syntax.md#background-concepts). For help with individual messages, see [Troubleshooting common indexer errors and warnings](cognitive-search-common-errors-warnings.md).

The following steps show you how to get information about a skill.

1. In **AI enrichment > Skill Graph**, select a skill. The Skill Details pane opens to the right.

1. Select **Executions** to show which inputs and outputs were used during skill execution.

   :::image type="content" source="media/cognitive-search-debug/skill-input-output-detection.png" alt-text="Screenshot of Skill graph, details, and execution tab inputs and outputs." border="true":::

1. Select **`</>`** Expression Evaluator to show the values returned by the skill.

## Next steps

Now that you understand the layout and capabilities of the Debug Sessions visual editor, try the tutorial for a hands-on experience.

> [!div class="nextstepaction"]
> [Explore Debug sessions feature tutorial](./cognitive-search-tutorial-debug-sessions.md)
