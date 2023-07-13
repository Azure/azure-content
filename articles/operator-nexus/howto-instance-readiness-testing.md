---
title: "Azure Operator Nexus: How to run Instance Readiness Testing"
description: Learn how to run instance readiness testing.
author: DannyMassa
ms.author: danielmassa
ms.service: azure-operator-nexus
ms.topic: how-to
ms.date: 07/13/2023
ms.custom: template-how-to, devx-track-azurecli
---

# Instance Resource Testing

Instance Readiness Testing (IRT) is a framework built to orchestrate real world workloads for testing on the Nexus For Operators Platform.

## Prerequisites

1. Linux environment (tested on Ubuntu)
1. Knowledge of networks to use for the test
    * Networks to use for the test will be specified in a "network-blueprint.jsonc" file, see Confirguration section below.
1. curl or wget to download IRT package

## Before Execution

1. Download nexus-irt.tar.gz from aka.ms/nexus-irt `curl -Lo nexus-irt.tar.gz aka.ms/nexus-irt`
1. Extract the tarball to the local file system: `mkdir -p irt && tar xf nexus-irt.tar.gz --directory ./irt`
1. Switch to the new directory `cd irt`
1. If needed run `setup.sh` to initially set up an environment.
    * `setup.sh` assumes a non-root user and attempts to use `sudo` which will install:
        1. `jq` version 1.6
        1. `yq` version 4.33
        1. `azcopy` version 10
        1. `az` Azure CLI minimum version not known, please stay up to date.
        1. `elinks` for viewing html files on the command line
        1. `tree` for viewing directory structures
        1. `moreutils` utilities for viewing progress from the ACI container
1. If desired, set up a storage account to archive test results over time. For help, see the [Instructions](#uploading-results-to-your-own-archive)
1. Log into azure, if not already logged in: `az login --use-device`
    * User should have `Contributor` role
1. Create an Azure Managed Identity for the container to use (Will default to CloudTest Managed Identity if not provided)
    * Using the provided script: `MI_RESOURCE_GROUP="<your resource group> MI_NAME="<managed identity name>" SUBSCRIPTION="<subscription>" ./create-managed-identity.sh`
    * Can be created manually va the Azure Portal, refer to the script for needed permissions
1. Create a service principal and aad secutiry group. The service principal will be used to make operations during the test, the group informs the kubernetes cluster of valid users, so the service principal must be a part of the aad secturiy group.
    * You can provide your own, or use our provided script, here's an example on how it could be executed; `AAD_GROUP_NAME=external-test-aad-group-8 SERVICE_PRINCIPAL_NAME=external-test-sp-8 ./irt/create-service-principal.sh`
    * This script will print four key value pairs for you to include in your input file, described below.
1. If necessary, create the Isolation Domains required to execute the tests. They are not lifecycled as part of this test scenario.
   * **Note:** if deploying isolation domains, your network blueprint must define at least one external network per isolation domain. see `networks-blueprint.example.yml` for help configuring your network blueprint.
   * `create-l3-isolation-domains.sh` takes one parameter, a path to your networks blueprint file; here's an example of the script being invoked:
     * `create-l3-isolation-domains.sh ./networks-blueprint.yml`

### Input Configuration

1. Build your input file. The IRT tarball provides `irt-input.example.yml` as an example. These values **will not work for all instances**, they need to be manually changed and the file also needs to be renamed to `irt-input.yml`
1. define the values of networks-blueprint input, an example of this file is given in networks-blueprint.example.yml

The network blueprint input schema for IRT is defined in the networks-blueprint.example.yml. Curretly IRT has a network requirement of:

1. Three(3) L3 Networks

   * Two of them with mtu 1500
   * one of them with mtu 9000 and should not have fabric_asn definition

1. One(1) Trunked Network
1. All Vlans should be greater than 500

## Execution

1. Execute: `./irt.sh irt-input.yml`
    * Assumes irt-input.yml is in the same location as irt.sh. If in a different location provide the full file path.

## Results

1. A file named `summary-<cluster_name>-<timestamp>.html` will be present after execution and will have all the testing results. It can be viewed:
    1. From any browser
    1. using elinks or lynx, i.e. `elinks summary-<cluster_name>-<timestamp>..html`, to view from the command line
    1. If you provide an SAS Token & URL for the `PUBLISH_RESULTS_TO` parameter, the archive storage container will contain your results, and can be previewed by navigating to the link presented to you at the end of the IRT run.

### Uploading Results to Your Own Archive

1. We offer a supplementary script, `create-archive-storage.sh` to allow you to setup a storage container to store your results. The script will generate a 3 day SAS Token for a storage container, if the container, account, or resource group do not exist, they will be created as part of the script.
   1. The script expects the following environment variables to be defined:
      1. RESOURCE_GROUP
      1. SUBSCRIPTION
      1. STORAGE_ACCOUNT_NAME
      1. STORAGE_CONTAINER_NAME
1. Copy the last output from the script, i.e. `PUBLISH_RESULTS_TO="<sas-token>"`, into your IRT YAML input. The value will be good for the life of the token.
