---
title: "使用 Azure CLI 在多個 IP 組態上進行負載平衡 | Microsoft Docs"
description: "了解如何使用 Azure CLI 將多個 IP 位址指派給虛擬機器 | Resource Manager。"
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 5c4e6fd9b560d3005294a02a5ec52c140690c819
ms.openlocfilehash: 79bb6d74c4eba99386b44e8464214d84abf882c6


---
# <a name="load-balancing-on-multiple-ip-configurations"></a>在多個 IP 組態上進行負載平衡

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
>

本文說明如何對每個虛擬網路介面 (NIC) 的多個 IP 位址使用 Azure Load Balancer。 NIC 上的多個 IP 位址支援目前是預覽版本中的一項功能。 如需詳細資訊，請參閱本文的[限制](#limitations)一節。 下列案例說明這項功能如何與 Azure Load Balancer 搭配運作。

在此案例中，我們有兩部執行 Windows 的 VM，每部各有一個 NIC。 每個 NIC 具有多個 IP 組態。 每個 VM 裝載 contoso.com 和 fabrikam.com 兩個網站。 每個網站繫結到 NIC 上的其中一個 IP 組態。 我們使用 Load Balancer 公開兩個前端 IP 位址，每個網站各使用其中一個，以將流量分散給網站的個別 IP 組態。 此案例會在兩個前端以及兩個後端集區 IP 位址使用相同的連接埠號碼。

![LB 案例影像](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="limitations"></a>限制

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

若要註冊以進行預覽，請傳送電子郵件給 [多個 IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) ，其中包含您的訂用帳戶 ID 與用途。


## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>在多個 IP 組態上進行負載平衡的步驟

請遵循下列步驟來達成本文所述案例︰

1. [安裝和設定 Azure CLI](../xplat-cli-install.md)，方法是遵循所連結文章內的步驟並登入 Azure 帳戶。
2. 如上所述[建立資源群組](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-groups-and-choose-deployment-locations) (稱為 contosofabrikam)。
 
    ```azurecli
    azure group create contosofabrikam westcentralus
    ```

3. 為兩個 VM [建立可用性集合](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set)。 在此案例中，請使用下列命令：

    ```azurecli
    azure availset create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. [建立虛擬網路](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet) (稱為 myVNet) 和子網路 (稱為 mySubnet)： 

    ```azurecli
    azure network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus

    azure network vnet subnet create --resource-group contosofabrikam --vnet-name myVnet --name mySubnet --address-prefix 10.0.0.0/24
    ```

5. [建立負載平衡器](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-load-balancer-and-ip-pools) (稱為 mylb)：

    ```azurecli
    azure network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. 針對負載平衡器的前端 IP 組態建立兩個動態公用 IP 位址︰

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. 分別建立兩個前端 IP 組態 contosofe 和 fabrikamfe︰

    ```azurecli
    azure network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    azure network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. 建立後端位址集區 - contosopool 和 fabrikampool、[探查](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-load-balancer-health-probe) - HTTP，和負載平衡規則 - HTTPc 和 HTTPf：

    ```azurecli
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    azure network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    azure network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    azure network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. 執行下列命令，然後檢查輸出以[確認負載平衡器](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#verify-the-load-balancer)已正確建立︰

    ```azurecli
    azure network lb show --resource-group contosofabrikam --name mylb
    ```

10. 為第一個虛擬機器 VM1 [建立公用 IP](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address-pip) (myPublicIp) 和[儲存體帳戶](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-storage-account) (mystorageaccont1)，如下所示︰

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    azure storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. 為 VM1 [建立網路介面](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-nic-to-use-with-the-linux-vm) (VM1-NIC)，新增第二個 IP 組態 (VM1-ipconfig2)，並[建立 VM](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-the-linux-vms)，如下所示︰

    ```azurecli
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic --ip-config-name VM1-ipconfig1 --public-ip-name myPublicIP --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    azure network nic ip-config create --resource-group contosofabrikam --nic-name myNic --name VM1-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    azure vm create --resource-group contosofabrikam --name VM1 --location westcentralus --os-type linux --nic-name myNic --vnet-name VNet1 --vnet-subnet-name Subnet1 --availset-name myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount1 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

12. 為第二個 VM 重複步驟 10 至 11：

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns785 --allocation-method Dynamic
    azure storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount3426
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 --ip-config-name VM2-ipconfig1 --public-ip-name myPublicIP2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    azure network nic ip-config create --resource-group contosofabrikam --nic-name myNic2 --name VM2-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    azure vm create --resource-group contosofabrikam --name VM2 --location westcentralus --os-type linux --nic-name myNic2 --vnet-name VNet1 --vnet-subnet-name Subnet1 --availset-name myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount2 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

13. 最後，您必須將 DNS 資源記錄設定為指向 Load Balancer 的個別前端 IP 位址。 您可以在 Azure DNS 中裝載網域。 如需搭配使用 Azure DNS 與 Load Balancer 的詳細資訊，請參閱[使用 Azure DNS 搭配其他 Azure 服務](../dns/dns-for-azure-services.md)。


<!--HONumber=Nov16_HO5-->


