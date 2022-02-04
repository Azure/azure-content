---
title: Configure static outbound IP
description: Configure NAT Gateway for Azure Container Instances workloads that use the NAT Gateway's public IP address for static egress
ms.topic: article
ms.date: 02/04/2021
---

# Configure a NAT Gateway for static IP address for outbound traffic from a container group

Setting up a [container group](container-instances-container-groups.md) with an external-facing IP address allows external clients to use the IP address to access a container in the group. For example, a browser can access a web app running in a container. However, currently a container group uses a different IP address for outbound traffic. This egress IP address isn't exposed programmatically, which makes container group monitoring and configuration of client firewall rules more complex.

This article provides steps to configure a container group in a [virtual network](container-instances-virtual-network-concepts.md) integrated with [NAT Gateway](../virtual-network/nat-gateway/nat-gateway.md). By configuring a NAT Gateway to SNAT a subnet address range delegated to ACI, you can identify outbound traffic from your container groups. The container group egress traffic will use the public IP address of the NAT Gateway. A single NAT Gateway can be used by multiple container groups deployed in the virtual network's subnet delegated to Azure Container Instances.

In this article you use the Azure CLI to create the resources for this scenario:

* Container groups deployed on a delegated subnet [in the virtual network](container-instances-vnet.md) 
* A NAT Gateway deployed in the network with a static public IP address

You then validate egress from example container groups through the NAT Gateway.

> [!NOTE]
> Azure Container Instances recommends using NAT Gateway for containerized workoads that have static egress but not static ingress requirements. For ACI architecture that supports both static ingress and egress, please see the following tutorial: [Use Azure Firewall for ingress and egress](container-instances-egress-ip-address.md)

## Deploy ACI in a virtual network

In a typical case, you might already have an Azure virtual network in which to deploy a container group. For demonstration purposes, the following commands create a virtual network and subnet when the container group is created. The subnet is delegated to Azure Container Instances. 

The container group runs a small web app from the `aci-helloworld` image. As shown in other articles in the documentation, this image packages a small web app written in Node.js that serves a static HTML page.

If you need one, first create an Azure resource group with the [az group create][az-group-create] command. For example:

```azurecli
az group create --name myResourceGroup --location eastus
```

To simplify the following command examples, use an environment variable for the resource group's name:

```console
export RESOURCE_GROUP_NAME=myResourceGroup
```

Create the container group with the [az container create][az-container-create] command:

```azurecli
az container create \
  --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

> [!TIP]
> Adjust the value of `--subnet address-prefix` for the IP address space you need in your subnet. The smallest supported subnet is /29, which provides eight IP addresses. Some IP addresses are reserved for use by Azure.

## Deploy NAT Gateway in network

In the following sections, use the Azure CLI to deploy an Azure NAT gateway in the virtual network. For background, see [Tutorial: Create a NAT Gateway using Azure CLI](../virtual-network/nat-gateway/tutorial-create-nat-gateway-cli.md).

First, use the [az network vnet public-ip create][az-network-public-ip-create] to create a public IP address for the NAT gateway. This will be used to access the Internet.

```azurecli
az network public-ip create \
  --name myPublicIP \
  --resource-group $RESOURCE_GROUP_NAME \
  --sku standard \
  --allocation static
```

Store the public IP address in a variable. We will use this later during the validation step. 

```azurecli
NG_PUBLIC_IP="$(az network public-ip show \
  --name myPublicIP \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress --output tsv)"
```

Use the following [az network nat gateway create][az-network-nat-gateway-create] to create a NAT Gateway that uses the public IP you created in the previous step.

```azurecli
az network nat gateway create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name myNATgateway \
  --public-ip-addresses myPublicIP \
  --idle-timeout 10       
```
## Configure NAT service for source subnet

We'll configure the source subnet **aci-subnet** to use a specific NAT gateway resource **myNATgateway** with [az network vnet subnet update][az-network-vnet-subnet-update]. This command wil active the NAT service on the specified subnet. 

```azurecli
az network vnet subnet update \
    --resource-group $RESOURCE_GROUP_NAME  \
    --vnet-name aci-vnet \
    --name aci-subnet \
    --nat-gateway myNATgateway
```

## Test egress from a container group

Test inbound access to the *appcontainer* running in the virtual network by browsing to the firewall's public IP address. Previously, you stored the public IP address in variable $NG_PUBLIC_IP:

Deploy the following sample container into the virtual network. When it runs, it sends a single HTTP request to `http://checkip.dyndns.org`, which displays the IP address of the sender (the egress IP address). If the application rule on the firewall is configured properly, the firewall's public IP address is returned.

```azurecli
az container create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress \
  --image mcr.microsoft.com/azuredocs/aci-tutorial-sidecar \
  --command-line "curl -s http://checkip.dyndns.org" \
  --restart-policy OnFailure \
  --vnet aci-vnet \
  --subnet aci-subnet
```

View the container logs to confirm the IP address is the same as the public IP address we created in the first step of the tutorial.

```azurecli
az container logs \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress 
```

Output is similar to:

```console
<html><head><title>Current IP Check</title></head><body>Current IP Address: 52.142.18.133</body></html>
```
This IP address should match the public IP address created in the in the first step of the tutorial. 

```Bash
echo $NG_PUBLIC_IP
```

## Next steps

In this article, you set up container groups in a virtual network behind an Azure NAT Gateway. By using this configuration, you set up a single, static IP address egress from Azure Container Instances container groups.

For more information about managing egress traffic, see the [Azure NAT Gateway](../nat-gateway/index.yml) documentation.

[az-group-create]: /cli/azure/group#az_group_create
[az-container-create]: /cli/azure/container#az_container_create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[az-network-public-ip-show]: /cli/azure/network/public-ip/#az_network_public_ip_show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_update
[az-container-exec]: /cli/azure/container#az_container_exec
