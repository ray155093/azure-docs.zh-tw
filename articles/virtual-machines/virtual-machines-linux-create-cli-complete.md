---
title: "使用 Azure CLI 2.0 預覽建立完整的 Linux 環境 | Microsoft Docs"
description: "使用 Azure CLI 2.0 (預覽) 從頭開始建立儲存體、Linux VM、虛擬網路和子網路、負載平衡器、NIC、公用 IP 以及網路安全性群組。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/8/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 95b924257c64a115728c66956d5ea38eb8764a35
ms.openlocfilehash: b02be35b0a3e97dbab32467eb8f654ea9609e7aa


---
# <a name="create-a-complete-linux-environment-by-using-the-azure-cli-20-preview"></a>使用 Azure CLI 2.0 (預覽) 建立完整的 Linux 環境
在這篇文章中，我們將建立一個簡單的網路，當中包含一個負載平衡器，以及一組對開發和簡單運算而言相當實用的 VM。 我們將以逐個命令的方式逐步完成程序命令，直到您具備兩個可供您透過網際網路從任何地方連線的有效、安全 Linux VM 為止。 然後您便可以繼續著手更複雜的網路和環境。

在過程中，您將了解 Resource Manager 部署模型提供給您的相依性階層，以及它提供多少功能。 在您了解系統建置的方式之後，您就可以使用 [Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)更快地建置系統。 此外，在您了解環境的組件彼此如何搭配運作之後，就可以更輕鬆地建立範本來將它們自動化。

此環境包含：

* 兩個位於可用性設定組內的 VM。
* 一個在連接埠 80 有負載平衡規則的負載平衡器。
* 可保護 VM 防止不必要流量的網路安全性群組 (NSG) 規則。

![基本環境概觀](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本
您可以使用下列其中一個 CLI 版本來完成工作︰

- [Azure CLI 1.0](virtual-machines-linux-create-cli-complete-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – 適用於傳統和資源管理部署模型的 CLI
- [Azure CLI 2.0 (預覽)](#quick-commands) - 適用於資源管理部署模型的新一代 CLI (本文章)

## <a name="quick-commands"></a>快速命令
如果您需要快速完成工作，下列章節詳細說明上傳 VM 至 Azure 的基本命令。 每個步驟的詳細資訊和內容可在文件其他地方找到，從[這裡](#detailed-walkthrough)開始。

在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包含 `myResourceGroup`、`mystorageaccount` 和 `myVM`。

若要建立此自訂環境，您需要安裝 [Azure CLI 2.0 (預覽)](/cli/azure/install-az-cli2)，並且使用 [az login](/cli/azure/#login) 登入 Azure 帳戶。

首先，使用 [az group create](/cli/azure/group#create)建立資源群組。 下列範例會在 `westeurope` 位置建立名為 `myResourceGroup` 的資源群組：

```azurecli
az group create --name myResourceGroup --location westeurope
```

使用 [az storage account create](/cli/azure/storage/account#create) 建立儲存體帳戶。 下列範例會建立名為 `mystorageaccount` 的儲存體帳戶。 (儲存體帳戶名稱必須是唯一的，因此請提供您自己的唯一名稱。)

```azurecli
az storage account create --resource-group myResourceGroup --location westeurope \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

使用 [az network vnet create](/cli/azure/network/vnet#create) 建立虛擬網路。 下列範例會建立名為 `myVnet` 的虛擬網路和名為 `mySubnet` 的子網路：

```azurecli
az network vnet create --resource-group myResourceGroup --location westeurope --name myVnet \
  --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

使用 [az network public-ip create](/cli/azure/network/public-ip#create) 建立公用 IP。 下列範例會建立名為 `myPublicIP` 的公用 IP，DNS 名稱為`mypublicdns`。 (DNS 名稱必須是唯一的，因此請提供您自己的唯一名稱。)

```azurecli
az network public-ip create --resource-group myResourceGroup --location westeurope \
  --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

使用 [az network lb create](/cli/azure/network/lb#create)建立負載平衡器。 下列範例：

- 建立名為 `myLoadBalancer` 的負載平衡器
- 關聯公用 IP `myPublicIP`
- 建立名為 `mySubnetPool` 的前端 IP 集區
- 建立名為 `myBackEndPool` 的後端 IP 集區

```azurecli
az network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer --public-ip-address myPublicIP \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool
```

使用 [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#create) 建立負載平衡器的 SSH 輸入網路位址轉譯 (NAT) 規則。 下列範例會建立兩個負載平衡器，`myLoadBalancerRuleSSH1` 和 `myLoadBalancerRuleSSH2`：

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 --protocol tcp \
  --frontend-port 4222 --backend-port 22 --frontend-ip-name myFrontEndPool
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22 --frontend-ip-name myFrontEndPool
```

使用 [az network lb probe create](/cli/azure/network/lb/probe#create)建立負載平衡器健全狀況探查。 下列範例會建立名為 `myHealthProbe` 的 TCP 探查：

```azurecli
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80 --interval 15 --threshold 4
```

使用 [az network lb rule create](/cli/azure/network/lb/rule#create) 建立負載平衡器的 Web 輸入 NAT 規則。 下列範例會建立名為 `myLoadBalancerRuleWeb` 的負載平衡器規則，並將其與 `myHealthProbe` 探查相關聯：

```azurecli
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myLoadBalancerRuleWeb --protocol tcp --frontend-port 80 --backend-port 80 \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool \
  --probe-name myHealthProbe
```

使用 [az network lb show](/cli/azure/network/lb#show) 確認負載平衡器、IP 集區，以及 NAT 規則：

```azurecli
az network lb show --resource-group myResourceGroup --name myLoadBalancer
```

使用 [az network nsg create](/cli/azure/network/nsg#create) 建立網路安全性群組。 下列範例會建立名為 `myNetworkSecurityGroup` 的網路安全性群組：

```azurecli
az network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#create)新增兩個網路安全性群組的輸入規則。 下列範例會建立兩個規則，名為 `myNetworkSecurityGroupRuleSSH` 和 `myNetworkSecurityGroupRuleHTTP`：

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleSSH \
  --protocol tcp --direction inbound --priority 1000 --source-address-prefix '*' \
  --source-port-range '*' --destination-address-prefix '*' --destination-port-range 22 \
  --access allow
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleHTTP \
  --protocol tcp --direction inbound --priority 1001 --source-address-prefix '*' \
  --source-port-range '*' --destination-address-prefix '*' --destination-port-range 80 \
  --access allow
```

使用 [az network nic create](/cli/azure/network/nic#create)建立第一個網路介面卡 (NIC)。 下列範例會建立名為`myNic1` 的 NIC，並將它附加至負載平衡器 `myLoadBalancer` 和適當集區，並也將其附加至 `myNetworkSecurityGroup`：

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic1 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH1
```

再次使用 **az network nic create**建立第二個 NIC。 下列範例會建立名為 `myNic2` 的 NIC：

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic2 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH2
```

使用 [az vm availability-set create](/cli/azure/vm/availability-set#create)建立可用性設定組。 下列範例會建立名為 `myAvailabilitySet` 的可用性設定組：

```azurecli
az vm availability-set create --resource-group myResourceGroup --location westeurope \
  --name myAvailabilitySet
```

使用 [az vm create](/cli/azure/vm#create)建立第一個 Linux VM。 下列範例會建立名為 `myVM1` 的 VM：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic1 \
    --vnet myVnet \
    --subnet-name mySubnet \
    --nsg myNetworkSecurityGroup \
    --storage-account mystorageaccount \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username ops
```

再次使用 **az vm create** 建立第二個 Linux VM。 下列範例會建立名為 `myVM2` 的 VM：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic2 \
    --vnet myVnet \
    --subnet-name mySubnet \
    --nsg myNetworkSecurityGroup \
    --storage-account mystorageaccount \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username ops
```

確認已使用 [az vm show](/cli/azure/vm#show)正確建置的一切：

```azurecli
az vm show --resource-group myResourceGroup --name myVM1
az vm show --resource-group myResourceGroup --name myVM2
```

使用 [az group export](/cli/azure/group#export) 將您的新環境匯出成範本，以便快速重新建立新的執行個體：

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

## <a name="detailed-walkthrough"></a>詳細的逐步解說
接下來的詳細步驟將說明您建置環境時每個命令的功能。 當您建置自己的自訂開發環境或生產環境時，這些概念會相當有用。

請確定您已安裝最新的 [Azure CLI 2.0 (預覽)](/cli/azure/install-az-cli2) 並使用 [az login](/cli/azure/#login) 登入 Azure 帳戶。

在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包含 `myResourceGroup`、`mystorageaccount` 和 `myVM`。

## <a name="create-resource-groups-and-choose-deployment-locations"></a>建立資源群組並選擇部署位置
Azure 資源群組是邏輯部署實體，當中包含用來啟用資源部署邏輯管理的組態資訊和中繼資料。 使用 [az group create](/cli/azure/group#create) 建立資源群組。 下列範例會在 `westeurope` 位置建立名為 `myResourceGroup` 的資源群組：

```azurecli
az group create --name myResourceGroup --location westeurope
```

根據預設，輸出是 JSON (JavaScript 物件標記法)。 例如，若要輸出為清單或資料表，請使用 [az configure --output](/cli/azure/#configure)。 您也可以將 `--output` 新增到任何命令，以輸出格式進行一次變更。 下列範例顯示來自 **az group create** 命令的 JSON 輸出：

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-storage-account"></a>建立儲存體帳戶
您需要儲存體帳戶來用於您的 VM 磁碟和任何您想要新增的額外資料磁碟。 您幾乎是在建立資源群組之後立即建立儲存體帳戶。

在這裡，我們使用 [az storage account create](/cli/azure/storage/account#create) 命令，其中會傳遞帳戶的位置、控制它的資源群組，以及您想要的儲存體支援類型。 下列範例會建立名為 `mystorageaccount` 的儲存體帳戶：

```azurecli
az storage account create --resource-group myResourceGroup --location westeurope \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

輸出：

```json
{
  "accessTier": null,
  "creationTime": "2016-12-07T17:59:50.090092+00:00",
  "customDomain": null,
  "encryption": null,
  "id": "/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "westeurope",
  "name": "mystorageaccount",
  "primaryEndpoints": {
    "blob": "https://mystorageaccount.blob.core.windows.net/",
    "file": "https://mystorageaccount.file.core.windows.net/",
    "queue": "https://mystorageaccount.queue.core.windows.net/",
    "table": "https://mystorageaccount.table.core.windows.net/"
  },
  "primaryLocation": "westeurope",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "secondaryEndpoints": null,
  "secondaryLocation": null,
  "sku": {
    "name": "Standard_LRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "Available",
  "statusOfSecondary": null,
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

若要使用 CLI 來調查儲存體帳戶，您必須先設定帳戶名稱和金鑰。 使用 [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string)。 以您選擇的名稱取代下列範例中的儲存體帳戶名稱︰

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(az storage account show-connection-string --resource-group myResourceGroup --name mystorageaccount --query connectionString)"
```

然後您可以使用 [az storage container list](/cli/azure/storage/container#list)檢視儲存體資訊：

```azurecli
az storage container list
```

輸出：

```azurecli
[
  {
    "metadata": null,
    "name": "vhds",
    "properties": {
      "etag": "\"0x8D41F912D472F94\"",
      "lastModified": "2016-12-08T17:39:35+00:00",
      "lease": {
        "duration": null,
        "state": null,
        "status": null
      },
      "leaseDuration": "infinite",
      "leaseState": "leased",
      "leaseStatus": "locked"
    }
  }
]
```

## <a name="create-a-virtual-network-and-subnet"></a>建立虛擬網路和子網路
接著，您將需要建立一個在 Azure 中執行的虛擬網路，以及一個可供您建立 VM 的子網路。 下列範例會使用 [az network vnet create](/cli/azure/network/vnet#create) 建立名為 `myVnet` 與 `192.168.0.0/16` 位址首碼的虛擬網路，以及名為 `mySubnet` 與位址首碼為`192.168.1.0/24`的子網路：

```azurecli
az network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefix 192.168.0.0/16 \
  --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

輸出會將子網路顯示為在虛擬網路內邏輯建立︰

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "westeurope",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>建立公用 IP 位址
現在，讓我們建立將指派給您負載平衡器的公用 IP 位址 (PIP)。 它可讓您使用 [az network public-ip create](/cli/azure/network/public-ip#create) 命令從網際網路連線到您的 VM。 由於預設位址是動態位址，因此我們將使用 `--domain-name-label` 選項在 **cloudapp.azure.com** 網域中建立具名的 DNS 項目。 下列範例會建立名為 `myPublicIP` 的公用 IP，DNS 名稱為`mypublicdns`。 因為 DNS 名稱必須是唯一的，因此請提供您自己的唯一 DNS 名稱︰

```azurecli
az network public-ip create --resource-group myResourceGroup --location westeurope \
  --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

輸出：

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.westeurope.cloudapp.azure.com",
      "reverseFqdn": ""
    },
    "idleTimeoutInMinutes": 4,
    "ipAddress": "52.174.48.109",
    "provisioningState": "Succeeded",
    "publicIPAllocationMethod": "Static",
    "resourceGuid": "78218510-ea54-42d7-b2c2-44773fc14af5"
  }
}
```

公用 IP 位址資源已經以邏輯方式配置，但尚未指派特定位址。 若要取得 IP 位址，您將需要一個負載平衡器，而我們尚未建立此負載平衡器。

## <a name="create-a-load-balancer-and-ip-pools"></a>建立負載平衡器和 IP 集區
在建立負載平衡器的情況下，您將可以把流量分散到多個 VM。 它也會藉由執行多個 VM 以在進行維護或發生大量負載時回應使用者要求，為您的應用程式提供備援。 下列範例會使用 [az network lb create](/cli/azure/network/lb#create) 建立名為 `myLoadBalancer`的負載平衡器、名為 `myFrontEndPool`的前端 IP 集區，並連結 `myPublicIP` 資源：

```azurecli
az network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEndPool
```

輸出：

```json
{
  "loadBalancer": {
    "backendAddressPools": [
      {
        "etag": "W/\"7a05df7a-5ee2-4581-b411-4b6f048ab291\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
        "name": "myLoadBalancerbepool",
        "properties": {
          "provisioningState": "Succeeded"
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "frontendIPConfigurations": [
      {
        "etag": "W/\"7a05df7a-5ee2-4581-b411-4b6f048ab291\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
        "name": "myFrontEndPool",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "provisioningState": "Succeeded",
          "publicIPAddress": {
            "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
            "resourceGroup": "myResourceGroup"
          }
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "inboundNatPools": [],
    "inboundNatRules": [],
    "loadBalancingRules": [],
    "outboundNatRules": [],
    "probes": [],
    "provisioningState": "Succeeded",
    "resourceGuid": "f4879d84-5ae8-4e06-8b9b-1419baa875d9"
  }
}
```

請注意我們如何使用 `--public-ip-address` 參數來傳入我們稍早建立的 `myPublicIP`。 將公用 IP 位址指派給負載平衡器可讓您透過網際網路連線到您的 VM。

我們將使用後端集區作為我們 VM 要連接的位置。 這樣一來，流量便可以經由負載平衡器流向 VM。 讓我們使用 [az network lb address-pool create](/cli/azure/network/lb/address-pool#create) 建立後端流量的 IP 集區。 下列範例會建立名為 `myBackEndPool` 的後端集區：

```azurecli
az network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

摘錄的輸出︰

```json
  "backendAddressPools": [
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
      "loadBalancingRules": null,
      "name": "myLoadBalancerbepool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    },
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
      "loadBalancingRules": null,
      "name": "myBackEndPool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    }
  ],
  "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
```


## <a name="create-load-balancer-nat-rules"></a>建立負載平衡器 NAT 規則
若要讓流量流經負載平衡器，我們需要建立指定輸入或輸出動作的網路位址轉譯 (NAT) 規則。 您可以指定要使用的通訊協定，然後依需要將外部連接埠對應到內部連接埠。 在我們的環境中，使用 [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#create) 建立一些規則以允許 SSH 透過負載平衡器流向 VM。 我們將設定 TCP 連接埠 4222 和 4223 以導向 VM 上的 TCP 連接埠 22 (我們會在稍後建立)。 下列範例會建立名為 `myLoadBalancerRuleSSH1` 的規則以將 TCP 連接埠 4222 對應至連接埠 22：

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 --protocol tcp \
  --frontend-port 4222 --backend-port 22 --frontend-ip-name myFrontEndPool
```

輸出：

```json
{
  "backendIpConfiguration": null,
  "backendPort": 22,
  "enableFloatingIp": false,
  "etag": "W/\"72843cf8-b5fb-4655-9ac8-9cbbbbf1205a\"",
  "frontendIpConfiguration": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "frontendPort": 4222,
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
  "idleTimeoutInMinutes": 4,
  "name": "myLoadBalancerRuleSSH1",
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup"
}
```

針對進行 SSH 的第二個 NAT 規則，重複此程序。 下列範例會建立名為 `myLoadBalancerRuleSSH2` 的規則以將 TCP 連接埠 4223 對應至連接埠 22：

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22 --frontend-ip-name myFrontEndPool
```

## <a name="create-a-load-balancer-health-probe"></a>建立負載平衡器健全狀況探查
健全狀況探查會定期檢查受負載平衡器保護的 VM，以確定它們依定義的方式運作及回應要求。 否則，就從將它們從作業中移除，以確保不會將使用者導向到它們。 您可以定義健全狀況探查的自訂檢查，以及間隔和逾時值。 如需有關健全狀況探查的詳細資訊，請參閱 [負載平衡器探查](../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 下列範例會使用 [az network lb probe create](/cli/azure/network/lb/probe#create) 建立名為 `myHealthProbe` 的 TCP 健全狀況探查：

```azurecli
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80 --interval 15 --threshold 4
```

輸出：

```json
{
  "etag": "W/\"757018f6-b70a-4651-b717-48b511d82ba0\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe",
  "intervalInSeconds": 15,
  "loadBalancingRules": null,
  "name": "myHealthProbe",
  "numberOfProbes": 4,
  "port": 80,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "requestPath": null,
  "resourceGroup": "myResourceGroup"
}
```

在這裡，我們指定了 15 秒的健全狀況檢查間隔。 最多可錯過&4; 次探查 (1 分鐘)，負載平衡器才會將該主機視為已不再運作。

讓我們繼續進行，並針對 web 流量建立 TCP 連接埠 80 的 NAT 規則，以將規則連結到我們的 IP 集區。 如果我們將規則連結到 IP 集區，而不是將規則個別連結到 VM，則我們可在 IP 集區中新增或移除 VM。 負載平衡器會自動調整流量的流動。 下列範例會使用 [az network lb rule create](/cli/azure/network/lb/rule#create) 來建立名為 `myLoadBalancerRuleWeb` 的規則，將 TCP 連接埠 80 對應至連接埠 80，並連結名為 `myHealthProbe` 的健全狀況探查：

```azurecli
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myLoadBalancerRuleWeb --protocol tcp --frontend-port 80 --backend-port 80 \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool \
  --probe-name myHealthProbe
```

輸出：

```json
{
  "backendAddressPool": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "backendPort": 80,
  "enableFloatingIp": false,
  "etag": "W/\"f0d77680-bf42-4d11-bfab-5d2c541bee56\"",
  "frontendIpConfiguration": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "frontendPort": 80,
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
  "idleTimeoutInMinutes": 4,
  "loadDistribution": "Default",
  "name": "myLoadBalancerRuleWeb",
  "probe": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe",
    "resourceGroup": "myResourceGroup"
  },
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="verify-the-load-balancer"></a>確認負載平衡器
現在，負載平衡器設定已完成。 以下是您所採取的步驟：

1. 您建立了負載平衡器。
2. 您建立了前端 IP 集區並為它指派公用 IP 位址。
3. 您建立了 VM 可以連接的後端 IP 集區。
4. 您建立了可允許透過 SSH 連接到 VM 以進行管理的 NAT 規則，以及可允許將 TCP 連接埠 80 用於 Web 應用程式的規則。
5. 您新增了健全狀況探查來定期檢查 VM。 這個健全狀況探查可確保使用者不會嘗試存取已不再運作或提供內容的 VM。

讓我們使用 [az network lb show](/cli/azure/network/lb#show) 檢閱您負載平衡器現在的樣子：

```azurecli
az network lb show --resource-group myResourceGroup --name myLoadBalancer
```

輸出：

```json
{
  "backendAddressPools": [
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
      "loadBalancingRules": null,
      "name": "myLoadBalancerbepool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    },
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
      "loadBalancingRules": null,
      "name": "myBackEndPool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    }
  ],
  "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
  "frontendIpConfigurations": [
    {
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/LoadBalancerFrontEnd",
      "inboundNatPools": null,
      "inboundNatRules": null,
      "loadBalancingRules": null,
      "name": "LoadBalancerFrontEnd",
      "outboundNatRules": null,
      "privateIpAddress": null,
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Succeeded",
      "publicIpAddress": {
        "dnsSettings": null,
        "etag": null,
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/PublicIPmyLoadBalancer",
        "idleTimeoutInMinutes": null,
        "ipAddress": null,
        "ipConfiguration": null,
        "location": null,
        "name": null,
        "provisioningState": null,
        "publicIpAddressVersion": null,
        "publicIpAllocationMethod": null,
        "resourceGroup": "myResourceGroup",
        "resourceGuid": null,
        "tags": null,
        "type": null
      },
      "resourceGroup": "myResourceGroup",
      "subnet": null
    },
    {
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
      "inboundNatPools": null,
      "inboundNatRules": null,
      "loadBalancingRules": null,
      "name": "myFrontEndPool",
      "outboundNatRules": null,
      "privateIpAddress": null,
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Succeeded",
      "publicIpAddress": {
        "dnsSettings": null,
        "etag": null,
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
        "idleTimeoutInMinutes": null,
        "ipAddress": null,
        "ipConfiguration": null,
        "location": null,
        "name": null,
        "provisioningState": null,
        "publicIpAddressVersion": null,
        "publicIpAllocationMethod": null,
        "resourceGroup": "myResourceGroup",
        "resourceGuid": null,
        "tags": null,
        "type": null
      },
      "resourceGroup": "myResourceGroup",
      "subnet": null
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "inboundNatPools": [],
  "inboundNatRules": [],
  "loadBalancingRules": [],
  "location": "westeurope",
  "name": "myLoadBalancer",
  "outboundNatRules": [],
  "probes": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "b5815801-b53d-4c22-aafc-2a9064f90f3c",
  "tags": {},
  "type": "Microsoft.Network/loadBalancers"
}
```

## <a name="create-a-network-security-group-and-rules"></a>建立網路安全性群組和規則
我們現在會建立網路安全性群組和管理 NIC 存取權的輸入規則。 網路安全性群組可以套用至 NIC 或子網路。 您要定義規則以控制進出 VM 的流量。 下列範例會使用 [az network nsg create](/cli/azure/network/nsg#create) 來建立名為 `myNetworkSecurityGroup` 的網路安全性群組：

```azurecli
az network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

讓我們使用 [az network nsg rule create](/cli/azure/network/nsg/rule#create) 新增 NSG 的輸入規則以允許連接埠 22 上的輸入連線 (以支援 SSH)。 下列範例會建立名為 `myNetworkSecurityGroupRuleSSH` 的規則以允許連接埠 22 上的 TCP︰

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleSSH \
  --protocol tcp --direction inbound --priority 1000 \
  --source-address-prefix '*' --source-port-range '*' \
  --destination-address-prefix '*' --destination-port-range 22 --access allow
```

現在讓我們加入 NSG 的輸入規則以允許連接埠 80 上的輸入連線 (以支援 web 流量)。 下列範例會建立名為 `myNetworkSecurityGroupRuleHTTP` 的規則以允許連接埠 80 上的 TCP︰

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleHTTP \
  --protocol tcp --direction inbound --priority 1001 \
  --source-address-prefix '*' --source-port-range '*' \
  --destination-address-prefix '*' --destination-port-range 80 --access allow
```

> [!NOTE]
> 輸入規則是輸入網路連線的篩選器。 在此範例中，我們將 NSG 繫結至 VM 虛擬 NIC，這意謂著任何傳送給連接埠 22 的要求都會傳遞到 VM 上的 NIC。 這個輸入規則與網路連線相關，而不是與端點 (在傳統部署中會相關的對象) 相關。 若要開啟連接埠，您必須將 `--source-port-range` 保留設定為 '\*' (預設)，才能接受來自**「任何」**要求連接埠的輸入要求。 連接埠通常是動態的。

使用 [az network nsg show](/cli/azure/network/nsg#show) 檢查網路安全性群組及規則：

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

輸出：

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBound",
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "westeurope",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "79c2c293-ee3e-4616-bf9c-4741ff1f708a",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleHTTP",
      "name": "myNetworkSecurityGroupRuleHTTP",
      "priority": 1001,
      "protocol": "tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": {},
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>建立要與 Linux VM 搭配使用的 NIC
您可以透過程式設計方式提供 NIC，因為您可以將規則套用到 NIC 的使用上。 您也可以有多個 NIC。 在下列 [az network nic create](https://docs.microsoft.com/en-us/cli/azure/network/nic#create) 命令中，您會將 NIC 連結到負載後端 IP 集區，並將它與 NAT 規則建立關聯以允許 SSH 流量和網路安全性群組。

下列範例會建立名為 `myNic1` 的 NIC：

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic1 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH1
```

輸出：

```json
{
  "newNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": []
    },
    "enableIPForwarding": false,
    "ipConfigurations": [
      {
        "etag": "W/\"a76b5c0d-14e1-4a99-afd4-5cd8ac0465ca\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/ipconfig1",
        "name": "ipconfig1",
        "properties": {
          "loadBalancerBackendAddressPools": [
            {
              "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
              "resourceGroup": "myResourceGroup"
            }
          ],
          "loadBalancerInboundNatRules": [
            {
              "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
              "resourceGroup": "myResourceGroup"
            }
          ],
          "primary": true,
          "privateIPAddress": "192.168.1.4",
          "privateIPAllocationMethod": "Dynamic",
          "provisioningState": "Succeeded",
          "subnet": {
            "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
            "resourceGroup": "myResourceGroup"
          }
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "networkSecurityGroup": {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "resourceGroup": "myResourceGroup"
    },
    "provisioningState": "Succeeded",
    "resourceGuid": "838977cb-cf4b-4c4a-9a32-0ddec7dc818b"
  }
}
```

現在，我們將建立的第二個 NIC，其中會再次將它連結到我們的後端 IP 集區。 這一次，第二個 NAT 規則將允許 SSH 流量。 下列範例會建立名為 `myNic2` 的 NIC：

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic2 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH2
```


## <a name="create-an-availability-set"></a>建立可用性設定組
可用性設定組可協助將 VM 分散到容錯網域和升級網域。 讓我們使用 [az vm availability-set create](/cli/azure/vm/availability-set#create) 來建立 VM 的可用性設定組。 下列範例會建立名為 `myAvailabilitySet` 的可用性設定組：

```azurecli
az vm availability-set create --resource-group myResourceGroup --location westeurope \
  --name myAvailabilitySet
```

容錯網域定義一個虛擬機器群組，且群組內的虛擬機器會共用通用電源和網路交換器。 根據預設，可用性設定組內設定的虛擬機器最多可分散到三個容錯網域。 這裡的概念是，其中一個容錯網域中的硬體問題將不會影響每個執行您應用程式的 VM。 將多個 VM 放在一個可用性設定組中時，Azure 會自動將它們分散到容錯網域。

升級網域表示虛擬機器群組和可同時重新啟動的基礎實體硬體。 在計劃性維護期間，可能不會循序重新啟動升級網域，而只會一次重新啟動一個升級網域。 同樣地，將多個 VM 放在一個可用性設定網站中時，Azure 會自動將它們分散到升級網域。

深入了解 [管理 VM 的可用性](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。


## <a name="create-the-linux-vms"></a>建立 Linux VM
您已建立儲存體和網路資源來支援可存取網際網路的 VM。 現在，讓我們建立這些 VM，並利用沒有密碼的 SSH 金鑰來保障其安全。 在此情況下，我們要根據最新的 LTS 建立 Ubuntu VM。 我們會使用 [az vm image list](/cli/azure/vm/image#list)來找出該映像資訊 (如 [尋找 Azure VM 映像](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)所述)。

我們也會指定要用於驗證的 SSH 金鑰。 如果您沒有任何 SSH 金鑰，可以使用[這些指示](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 來建立它們。 或者，您也可以使用 `--admin-password` 方法，在 VM 建立後驗證 SSH 連線。 這個方法通常較不安全。

我們會使用 [az vm create](/cli/azure/vm#create) 命令將所有資源和資訊結合在一起來建立 VM：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic1 \
    --vnet myVnet \
    --subnet-name mySubnet \
    --nsg myNetworkSecurityGroup \
    --storage-account mystorageaccount \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username ops
```

輸出：

```json
{
  "fqdn": "",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1",
  "macAddress": "",
  "privateIpAddress": "",
  "publicIpAddress": "",
  "resourceGroup": "myResourceGroup"
}
```

您可以使用預設 SSH 金鑰來立即連線到 VM。 請確定您指定的連接埠正確，因為我們要通過的是負載平衡器。 (針對第一個 VM，我們設定了將連接埠 4222 轉送到 VM 的 NAT 規則。)

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222 -i ~/.ssh/id_rsa.pub
```

輸出：

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

繼續進行，並以相同方式建立第二個 VM：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic2 \
    --vnet myVnet \
    --subnet-name mySubnet \
    --nsg myNetworkSecurityGroup \
    --storage-account mystorageaccount \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username ops
```

此時，您的 Ubuntu VM 是在 Azure 中的負載平衡器後方執行，您只能利用 SSH 金鑰組來登入 (因為密碼已被停用)。 您可以安裝 nginx 或 httpd、部署 Web 應用程式，然後查看經由負載平衡器流向兩個 VM 的流量。


## <a name="export-the-environment-as-a-template"></a>將環境匯出為範本
您現在已經建立這個環境，如果您想要使用相同的參數來建立其他開發環境，或想要建立與其相符的生產環境時，該怎麼辦？ Resource Manager 可使用定義了所有環境參數的 JSON 範本。 您可以藉由參考此 JSON 範本來建置整個環境。 您可以[手動建立 JSON 範本](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)，或將現有的環境匯出來為您建立 JSON 範本。 使用 [az group export](/cli/azure/group#export) 來匯出您的資源群組，如下所示︰

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

此命令會在您目前的工作目錄中建立 `myResourceGroup.json` 檔案。 當您從這個範本建立環境時，系統會提示您輸入所有資源名稱，包括負載平衡器、網路介面或 VM 的名稱。 您可以藉由將 `--include-parameter-default-value` 參數新增至稍早所示的 **az group export** 命令中，在您的範本檔案中填入這些名稱。 請編輯您的 JSON 範本以指定資源名稱，或 [建立 parameters.json 檔案](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 來指定資源名稱。

若要從您的範本建立環境，請使用 [az group deployment create](/cli/azure/group/deployment#create)，如下所示︰

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

您可以 [深入了解如何從範本進行部署](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 請了解如何以累加方式更新環境、使用參數檔案，以及從單一儲存體位置存取範本。

## <a name="next-steps"></a>後續步驟
現在您已準備好開始使用多個網路元件和 VM。 您可以利用這裡介紹的核心元件，使用這個範例環境來建置您的應用程式。



<!--HONumber=Jan17_HO1-->


