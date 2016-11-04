
---
title: 使用 Azure CLI 建立完整的 Linux 環境 | Microsoft Docs
description: 使用 Azure CLI 從頭開始建立儲存體、Linux VM、虛擬網路和子網路、負載平衡器、NIC、公用 IP 以及網路安全性群組。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: iainfou

---
# 使用 Azure CLI 建立完整的 Linux 環境
在這篇文章中，我們將建立一個簡單的網路，當中包含一個負載平衡器，以及一組對開發和簡單運算而言相當實用的 VM。我們將以逐個命令的方式逐步完成程序命令，直到您具備兩個可供您透過網際網路從任何地方連線的有效、安全 Linux VM 為止。然後您便可以繼續著手更複雜的網路和環境。

在過程中，您將了解 Resource Manager 部署模型提供給您的相依性階層，以及它提供多少功能。在您了解系統建置的方式之後，您就可以使用 [Azure Resource Manager 範本](../resource-group-authoring-templates.md)更快地建置系統。此外，在您了解環境的組件彼此如何搭配運作之後，就可以更輕鬆地建立範本來將它們自動化。

此環境包含：

* 兩個位於可用性設定組內的 VM。
* 一個在連接埠 80 有負載平衡規則的負載平衡器。
* 可保護 VM 防止不必要流量的網路安全性群組 (NSG) 規則。

![基本環境概觀](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

若要建立此自訂環境，您需要處於 Resource Manager 模式 (`azure config mode arm`) 的最新 [Azure CLI](../xplat-cli-install.md) 。您也需要 JSON 剖析工具。此範例使用 [jq](https://stedolan.github.io/jq/)。

## 快速命令
您可以使用下列快速命令來建置您的自訂環境。如需有關您建置環境時每個命令功能的詳細資訊，請參閱[下面的詳細逐步解說步驟](#detailed-walkthrough)。

建立資源群組：

```bash
azure group create TestRG -l westeurope
```

使用 JSON 剖析器來確認資源群組：

```bash
azure group show TestRG --json | jq '.'
```

建立儲存體帳戶：

```bash
azure storage account create -g TestRG -l westeurope --kind Storage --sku-name GRS computeteststore
```

使用 JSON 剖析器來確認儲存體帳戶：

```bash
azure storage account show -g TestRG computeteststore --json | jq '.'
```

建立虛擬網路：

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

建立子網路：

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

使用 JSON 剖析器來確認虛擬網路和子網路：

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

建立公用 IP：

```bash
azure network public-ip create -g TestRG -n TestLBPIP -l westeurope -d testlb -a static -i 4
```

建立負載平衡器：

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

建立負載平衡器的前端 IP 集區，並與公用 IP 建立關聯：

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

建立負載平衡器的後端 IP 集區：

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

建立負載平衡器的 SSH 輸入 NAT 規則：

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM2-SSH -p tcp -f 4223 -b 22
```

建立負載平衡器的 Web 輸入 NAT 規則：

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

建立負載平衡器健全狀況探查：

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "tcp" -i 15 -c 4
```

使用 JSON 剖析器來確認負載平衡器、IP 集區及 NAT 規則：

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

建立第一個網路介面卡 (NIC)：

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool" \
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
```

建立第二個 NIC：

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool" \
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH"
```

使用 JSON 剖析器來確認 NIC：

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
azure network nic show TestRG LB-NIC2 --json | jq '.'
```

建立 NSG：

```bash
azure network nsg create -g TestRG -n TestNSG -l westeurope
```

為 NSG 新增輸入規則：

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 22 --access allow -g TestRG -a TestNSG -n SSHRule
azure network nsg rule create --protocol tcp --direction inbound --priority 1001 \
    --destination-port-range 80 --access allow -g TestRG -a TestNSG -n HTTPRule
```

使用 JSON 剖析器來確認 NSG 和輸入規則：

```bash
azure network nsg show -g TestRG -n TestNSG --json | jq '.'
```

將 NSG 繫結至 NIC：

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

建立可用性設定組：

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

建立第一個 Linux VM：

```bash
azure vm create \
    --resource-group TestRG \
    --name TestVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC1 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

建立第二個 Linux VM：

```bash
azure vm create \
    --resource-group TestRG \
    --name TestVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC2 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

使用 JSON 剖析器來確認已建置的所有項目︰

```bash
azure vm show -g TestRG -n TestVM1 --json | jq '.'
azure vm show -g TestRG -n TestVM2 --json | jq '.'
```

將您建置的環境匯出成範本，以便快速重新建立新的執行個體：

```bash
azure resource export TestRG
```

## 詳細的逐步解說
接下來的詳細步驟將說明您建置環境時每個命令的功能。當您建置自己的自訂開發環境或生產環境時，這些概念會相當有用。

## 建立資源群組並選擇部署位置
Azure 資源群組是邏輯部署實體，當中包含用來啟用資源部署邏輯管理的組態資訊和中繼資料。

```bash
azure group create TestRG westeurope
```

輸出：

```bash                        
info:    Executing command group create
+ Getting resource group TestRG
+ Creating resource group TestRG
info:    Created resource group TestRG
data:    Id:                  /subscriptions/<yoursub>/resourceGroups/TestRG
data:    Name:                TestRG
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## 建立儲存體帳戶
您需要儲存體帳戶來用於您的 VM 磁碟和任何您想要新增的額外資料磁碟。您幾乎是在建立資源群組之後立即建立儲存體帳戶。

在這裡，我們使用 `azure storage account create` 命令，其中會傳遞帳戶的位置、控制它的資源群組，以及您想要的儲存體支援類型。

```bash
azure storage account create \  
--location westeurope \
--resource-group TestRG \
--kind Storage --sku-name GRS \
computeteststore
```

輸出：

```bash
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
ahmet•~/workspace/keygen» azure group show testrg
info:    Executing command group show
+ Listing resource groups
+ Listing resources for the group
data:    Id:                  /subscriptions/<guid>/resourceGroups/TestRG
data:    Name:                TestRG
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:    Resources:
data:
data:      Id      : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore
data:      Name    : computeteststore
data:      Type    : storageAccounts
data:      Location: westeurope
data:      Tags    :
data:
data:    Permissions:
data:      Actions: *
data:      NotActions:
data:
info:    group show command OK
```

若要使用 `azure group show` 命令來檢查我們的資源群組，讓我們使用 [jq](https://stedolan.github.io/jq/) 工具來搭配 `--json` Azure CLI 選項。(您可以使用 **jsawk** 或任何您慣用的語言程式庫來剖析 JSON。)

```bash
azure group show TestRG --json | jq                                                                                      
```


輸出：

```bash
{
  "tags": {},
  "id": "/subscriptions/<guid>/resourceGroups/TestRG",
  "name": "TestRG",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
      "name": "computeteststore",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

若要使用 CLI 來調查儲存體帳戶，您必須先使用下列命令的變化來設定帳戶名稱和金鑰。以您選擇的名稱取代下列範例中的儲存體帳戶名稱︰

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show computeteststore --resource-group testrg --json | jq -r '.string')"
```

然後您就可以輕鬆地檢視您的儲存體資訊︰

```bash
azure storage container list
```

輸出：

```bash
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## 建立虛擬網路和子網路
接著，您將需要建立一個在 Azure 中執行的虛擬網路，以及一個可供您安裝 VM 的子網路。

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

輸出：

```bash
info:    Executing command network vnet create
+ Looking up virtual network "TestVNet"
+ Creating virtual network "TestVNet"
+ Loading virtual network state
data:    Id                              : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
data:    Name                            : TestVNet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

同樣地，讓我們使用 --json 選項 `azure group show` 和 **jq** 來查看我們如何建置資源。我們現在有 `storageAccounts` 資源和 `virtualNetworks` 資源。

```bash
azure group show TestRG --json | jq '.'
```

輸出：

```bash
{
  "tags": {},
  "id": "/subscriptions/<guid>/resourceGroups/TestRG",
  "name": "TestRG",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
      "name": "TestVNet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
      "name": "computeteststore",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

現在讓我們在要於其中部署 VM 的 `TestVnet` 虛擬網路中建立子網路。我們將使用 `azure network vnet subnet create` 命令搭配我們已經建立的資源︰`TestRG` 資源群組和 `TestVNet` 虛擬網路。我們將新增子網路名稱 `FrontEnd` 和子網路位址首碼 `192.168.1.0/24`，如下：

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

輸出：

```bash
info:    Executing command network vnet subnet create
+ Looking up the subnet "FrontEnd"
+ Creating subnet "FrontEnd"
+ Looking up the subnet "FrontEnd"
data:    Id                              : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : FrontEnd
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

由於子網路是以邏輯方式位於虛擬網路內，因此我們將使用略為不同的命令來尋找子網路資訊。我們使用的命令是 `azure network vnet show`，但是我們將繼續使用 **jq** 來檢查 JSON 輸出。

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

輸出：

```bash
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "FrontEnd",
      "etag": "W/"974f3e2c-028e-4b35-832b-a4b16ad25eb6"",
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/"974f3e2c-028e-4b35-832b-a4b16ad25eb6"",
  "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
  "name": "TestVNet",
  "location": "westeurope"
}
```

## 建立公用 IP 位址 (PIP)
現在，讓我們建立將指派給您負載平衡器的公用 IP 位址 (PIP)。它可讓您使用 `azure network public-ip create` 命令從網際網路連線到您的 VM。由於預設位址是動態位址，因此我們將使用 `-d testsubdomain` 選項在 **cloudapp.azure.com** 網域中建立具名的 DNS 項目。

```bash
azure network public-ip create -d testsubdomain TestRG TestPIP westeurope
```

輸出：

```bash
info:    Executing command network public-ip create
+ Looking up the public ip "TestPIP"
+ Creating public ip address "TestPIP"
+ Looking up the public ip "TestPIP"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP
data:    Name                            : TestPIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : testsubdomain
data:    FQDN                            : testsubdomain.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

公用 IP 位址也是最上層資源，因此您可以使用 `azure group show` 來查看它。

```bash
azure group show TestRG --json | jq '.'
```

輸出：

```bash
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/TestRG",
"name": "TestRG",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
    "name": "TestNIC",
    "type": "networkInterfaces",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip",
    "name": "testpip",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
    "name": "TestVNet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
    "name": "computeteststore",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

您可以使用完整的 `azure network public-ip show` 命令來調查更多資源詳細資料，包括子網域的完整網域名稱 (FQDN)。公用 IP 位址資源已經以邏輯方式配置，但尚未指派特定位址。若要取得 IP 位址，您將需要一個負載平衡器，而我們尚未建立此負載平衡器。

```bash
azure network public-ip show TestRG TestPIP --json | jq '.'
```

輸出：

```bash
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "testsubdomain",
    "fqdn": "testsubdomain.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/"c63154b3-1130-49b9-a887-877d74d5ebc5"",
"id": "/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip",
"name": "testpip",
"location": "westeurope"
}
```

## 建立負載平衡器和 IP 集區
在建立負載平衡器的情況下，您將可以把流量分散到多個 VM。它也會藉由執行多個 VM 以在進行維護或發生大量負載時回應使用者要求，為您的應用程式提供備援。

我們會使用下列項目來建立負載平衡器：

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

輸出：

```bash
info:    Executing command network lb create
+ Looking up the load balancer "TestLB"
+ Creating load balancer "TestLB"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB
data:    Name                            : TestLB
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```
我們的負載平衡器很空，因此讓我們建立一些 IP 集區。我們想要為負載平衡器建立兩個 IP 集區：一個用於前端，一個用於後端。前端 IP 集區會公開顯示。它也是我們指派稍早所建立 PIP 的位置。然後，我們將使用後端集區作為我們 VM 要連接的位置。這樣一來，流量便可以經由負載平衡器流向 VM。

首先，讓我們建立前端 IP 集區︰

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

輸出：

```bash
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "TestLB"
+ Looking up the public ip "TestLBPIP"
+ Updating load balancer "TestLB"
data:    Name                            : TestFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP
info:    network lb frontend-ip create command OK
```

請注意我們如何使用 `--public-ip-name` 參數來傳入我們稍早建立的 TestLBPIP。將公用 IP 位址指派給負載平衡器可讓您透過網際網路連線到您的 VM。

接下來，讓我們建立第二個 IP 集區，這次是針對我們的後端流量︰

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

輸出：

```bash
info:    Executing command network lb address-pool create
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : TestBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

我們可以使用 `azure network lb show` 並檢查 JSON 輸出，來查看負載平衡器的執行情況：

```bash
azure network lb show TestRG TestLB --json | jq '.'
```

輸出：

```bash
{
  "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB",
  "name": "TestLB",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "frontendIPConfigurations": [
    {
      "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
      "name": "TestFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
      "name": "TestBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## 建立負載平衡器 NAT 規則
若要讓流量流經負載平衡器，我們需要建立指定輸入或輸出動作的 NAT 規則。您可以指定要使用的通訊協定，然後依需要將外部連接埠對應到內部連接埠。在我們的環境中，建立一些規則以允許 SSH 透過負載平衡器流向 VM。我們將設定 TCP 連接埠 4222 和 4223 以導向 VM 上的 TCP 連接埠 22 (我們會在稍後建立)：

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
```

輸出：

```bash
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "TestLB"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default frontend IP configuration "TestFrontEndPool"
+ Updating load balancer "TestLB"
data:    Name                            : VM1-SSH
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Frontend port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    Frontend IP configuration id    : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

針對進行 SSH 的第二個 NAT 規則，重複此程序︰

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM2-SSH -p tcp -f 4223 -b 22
```

讓我們繼續進行，並建立 TCP 連接埠 80 的 NAT 規則，以將規則連結到我們的 IP 集區。如果我們將規則連結到 IP 集區，而不是將規則個別連結到 VM，則我們只要在 IP 集區中新增或移除 VM 即可。然後負載平衡器就會自動調整流量的流動：

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

輸出：

```bash
info:    Executing command network lb rule create
+ Looking up the load balancer "TestLB"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "TestLB"
data:    Name                            : WebRule
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Frontend port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    Frontend IP configuration id    : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool
info:    network lb rule create command OK
```

## 建立負載平衡器健全狀況探查
健全狀況探查會定期檢查受負載平衡器保護的 VM，以確定它們依定義的方式運作及回應要求。否則，就從將它們從作業中移除，以確保不會將使用者導向到它們。您可以定義健全狀況探查的自訂檢查，以及間隔和逾時值。如需有關健全狀況探查的詳細資訊，請參閱[負載平衡器探查](../load-balancer/load-balancer-custom-probe-overview.md)。

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "tcp" -i 15 -c 4
```

輸出：

```bash
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : HealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

在這裡，我們指定了 15 秒的健全狀況檢查間隔。最多可錯過 4 次探查 (1 分鐘)，負載平衡器才會將該主機視為已不再運作。

## 確認負載平衡器
現在，負載平衡器設定已完成。以下是您所採取的步驟：

1. 首先，您建立了負載平衡器。
2. 然後，您建立了前端 IP 集區並為它指派公用 IP 位址。
3. 接著，您建立了 VM 可以連接的後端 IP 集區。
4. 然後，您建立了可允許透過 SSH 連接到 VM 以進行管理的 NAT 規則，以及可允許將 TCP 連接埠 80 用於 Web 應用程式的規則。
5. 最後，您新增了健全狀況探查來定期檢查 VM。這個健全狀況探查可確保使用者不會嘗試存取已不再運作或提供內容的 VM。

讓我們檢閱您負載平衡器現在的樣子︰

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

輸出：

```bash
{
  "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "VM1-SSH",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "protocol": "Tcp",
      "frontendPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "VM2-SSH",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "protocol": "Tcp",
      "frontendPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB",
  "name": "TestLB",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "frontendIPConfigurations": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "TestFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "TestBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "WebRule",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "frontendPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "HealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/probes/HealthProbe"
    }
  ]
}
```

## 建立要與 Linux VM 搭配使用的 NIC
 您可以透過程式設計方式提供 NIC，因為您可以將規則套用到 NIC 的使用上。您也可以有多個 NIC。在下列 `azure network nic create` 命令中，您會將 NIC 連結到負載後端 IP 集區，並將它與 NAT 規則建立關聯以允許 SSH 流量。若要這麼做，您需要指定您 Azure 訂用帳戶的訂用帳戶識別碼來取代 `<GUID>`：

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
     -d /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool \
     -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH
```

輸出：

```bash
info:    Executing command network nic create
+ Looking up the subnet "FrontEnd"
+ Looking up the network interface "LB-NIC1"
+ Creating network interface "LB-NIC1"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1
data:    Name                            : LB-NIC1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH
data:
info:    network nic create command OK
```

您可以直接檢查資源來查看詳細資料。您可以使用 `azure network nic show` 命令來檢查資源︰

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
```

輸出：

```bash
{
  "etag": "W/"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1",
  "name": "LB-NIC1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a"",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

現在，我們將建立的第二個 NIC，其中會再次將它連結到我們的後端 IP 集區。這一次，第二個 NAT 規則將允許 SSH 流量︰

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d  /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool \
    -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH
```

## 建立網路安全性群組和規則
現在，我們將建立 NSG 和管理對 NIC 之存取權的輸入規則。

```bash
azure network nsg create TestRG TestNSG westeurope
```

讓我們加入 NSG 的輸入規則以允許連接埠 22 上的輸入連線 (以支援 SSH)：

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 22 --access allow TestRG TestNSG SSHRule
```

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1001 \
    --destination-port-range 80 --access allow -g TestRG -a TestNSG -n HTTPRule
```

> [!NOTE]
> 輸入規則是輸入網路連線的篩選器。在此範例中，我們將 NSG 繫結至 VM 虛擬 NIC，這意謂著任何傳送給連接埠 22 的要求都會傳遞到 VM 上的 NIC。這個輸入規則與網路連線相關，而不是與端點 (在傳統部署中會相關的對象) 相關。若要開啟連接埠，您必須將 `--source-port-range` 保留設定為 '*' (預設)，才能接受來自「任何」要求連接埠的輸入要求。連接埠通常是動態的。
> 
> 

## 繫結至 NIC
將 NSG 繫結至 NIC：

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
```

```bash
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

## 建立可用性設定組
可用性設定組可協助將 VM 分散到容錯網域和升級網域。為 VM 建立可用性設定組：

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

容錯網域定義一個虛擬機器群組，且群組內的虛擬機器會共用通用電源和網路交換器。根據預設，可用性設定組內設定的虛擬機器最多可分散到三個容錯網域。這裡的概念是，其中一個容錯網域中的硬體問題將不會影響每個執行您應用程式的 VM。將多個 VM 放在一個可用性設定組中時，Azure 會自動將它們分散到容錯網域。

升級網域表示虛擬機器群組和可同時重新啟動的基礎實體硬體。在計劃性維護期間，可能不會循序重新啟動升級網域，而只會一次重新啟動一個升級網域。同樣地，將多個 VM 放在一個可用性設定網站中時，Azure 會自動將它們分散到升級網域。

深入了解[管理 VM 的可用性](virtual-machines-linux-manage-availability.md)。

## 建立 Linux VM
您已建立儲存體和網路資源來支援可存取網際網路的 VM。現在，讓我們建立這些 VM，並利用沒有密碼的 SSH 金鑰來保障其安全。在此情況下，我們要根據最新的 LTS 建立 Ubuntu VM。我們會使用 `azure vm image list` 來找出該映像資訊 (如[尋找 Azure VM 映像](virtual-machines-linux-cli-ps-findimage.md)所述)。

我們之前使用 `azure vm image list westeurope canonical | grep LTS` 命令來選取映像。在此案例中，我們會使用 `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`。針對最後一個欄位，我們會傳遞 `latest`，如此在未來我們就一律會取得最新的組建。(我們使用的字串是 `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`)。

對於任何已經使用 **ssh-keygen -t rsa -b 2048** 在 Linux 或 Mac 上建立 ssh rsa 公用和私密金鑰組的人來說，這下一個步驟都是相當熟悉的步驟。如果您的 `~/.ssh` 目錄中沒有任何憑證金鑰組，您可以建立它們︰

* 藉由使用 `azure vm create --generate-ssh-keys` 選項來自動建立。
* 藉由使用[自行建立的指示](virtual-machines-linux-mac-create-ssh-keys.md)來手動建立。

或者，您也可以使用 --admin-password 方法，在 VM 建立後驗證 SSH 連線。這個方法通常較不安全。

我們會使用 `azure vm create` 命令將所有資源和資訊結合在一起來建立 VM：

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC1 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

輸出：

```bash
info:    Executing command vm create
+ Looking up the VM "TestVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account computeteststore
+ Looking up the availability set "TestAvailSet"
info:    Found an Availability set "TestAvailSet"
+ Looking up the NIC "LB-NIC1"
info:    Found an existing NIC "LB-NIC1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "LB-NIC1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://computeteststore.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

您可以使用預設 SSH 金鑰來立即連線到 VM。請確定您指定的連接埠正確，因為我們要通過的是負載平衡器。(針對第一個 VM，我們設定了將連接埠 4222 轉送到 VM 的 NAT 規則)︰

```bash
 ssh ops@testlb.westeurope.cloudapp.azure.com -p 4222
```

輸出：

```bash
The authenticity of host '[testlb.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[testlb.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

ops@TestVM1:~$
```

繼續進行，並以相同方式建立第二個 VM：

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC2 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

您現在可以使用 `azure vm show testrg testvm` 命令來檢查您已建立的項目。此時，您的 Ubuntu VM 是在 Azure 中的負載平衡器後方執行，您只能利用 SSH 金鑰組來登入 (因為密碼已被停用)。您可以安裝 nginx 或 httpd、部署 Web 應用程式，然後查看經由負載平衡器流向兩個 VM 的流量。

```bash
azure vm show TestRG TestVM1
```

輸出：

```bash
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "LB-NIC1"
data:    Id                              :/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/TestVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :TestVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://computeteststore.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :TestVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LB-NIC1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/TESTAVAILSET
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://computeteststore.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## 將環境匯出為範本
您現在已經建立這個環境，如果您想要使用相同的參數來建立其他開發環境，或想要建立與其相符的生產環境時，該怎麼辦？ Resource Manager 可使用定義了所有環境參數的 JSON 範本。您可以藉由參考此 JSON 範本來建置整個環境。您可以[手動建立 JSON 範本](../resource-group-authoring-templates.md)，或直接將現有的環境匯出來為您建立 JSON 範本：

```bash
azure group export TestRG
```

此命令會在您目前的工作目錄中建立 `TestRG.json` 檔案。當您從這個範本建立環境時，系統會提示您輸入所有資源名稱，包括負載平衡器、網路介面或 VM 的名稱。您可以藉由將 `-p` 或 `--includeParameterDefaultValue` 參數新增至稍早所示的 `azure group export` 命令中，在您的範本檔案中填入這些名稱。請編輯您的 JSON 範本以指定資源名稱，或[建立 parameters.json 檔案](../resource-group-authoring-templates.md#parameters)來指定資源名稱。

從範本建立環境：

```bash
azure group deployment create -f TestRG.json -g NewRGFromTemplate
```

您可以[深入了解如何從範本進行部署](../resource-group-template-deploy-cli.md)。請了解如何以累加方式更新環境、使用參數檔案，以及從單一儲存體位置存取範本。

## 後續步驟
現在您已準備好開始使用多個網路元件和 VM。您可以利用這裡介紹的核心元件，使用這個範例環境來建置您的應用程式。

<!---HONumber=AcomDC_0914_2016-->