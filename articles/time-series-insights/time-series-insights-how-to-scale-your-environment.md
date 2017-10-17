---
title: How to scale your Azure Time Series Insights environment | Microsoft Docs
description: This tutorial covers how to scale your Azure Time Series Insights environment
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun

ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
---
# How to scale your Time Series Insights environment

This tutorial covers how to scale your Time Series Insights environment.

> [!NOTE]
> Scale up across sku types is not allowed. An environment with a S1 Sku cannot be converted into an S2 environment.

## Changing the capacity of your environment

1. In the Azure portal, type "Time Series" into the search bar.
2. Select Time Series Insights Environment from the returned list of items.
3. Select the environment whose capacity you want to change.
4. Under Settings, click Configure.
5. Use the Capacity slider to select the capacity that meets the requirements for your ingress rates and storage capacity (see rates and capacities below).

Capacities scale linearly, so a S1 sku with capacity 2 supports 2 GB (2 million) events per day ingress rate and 60 GB (60 million events) per month.

## S1 SKU ingress rates and capacities

| S1 SKU Capacity | Ingress Rate | Maximum Storage Capacity
| --- | --- | --- |
| 1 | 1 GB (1 million events) | 30 GB (30 million events) per month |
| 10 | 10 GB (10 million events) | 300 GB (300 million events) per month |

## S2 SKU ingress rates and capacities

| S2 SKU Capacity | Ingress Rate | Maximum Storage Capacity
| --- | --- | --- |
| 1 | 10 GB (10 million events) | 300 GB (300 million events) per month |
| 10 | 100 GB (100 million events) | 3 TB (3 billion events) per month |





## Next steps

* Verify that the new capacity is sufficient to prevent throttling. For more details, see the *Your environment might be getting throttled* section [here](time-series-insights-diagnose-and-solve-problems.md).
