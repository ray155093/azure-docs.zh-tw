<properties
   pageTitle="使用 Azure CLI 建立完整的 Linux 環境 | Microsoft Azure"
   description="使用 Azure CLI 從頭開始建立 Linux VM、儲存體、虛擬網路和子網路、負載平衡器、NIC、公用 IP、網路安全性群組。"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="iainfoulds"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="06/10/2016"
   ms.author="iainfou"/>

# 使用 Azure CLI 建立完整的 Linux 環境

讓我們來建立簡單的網路，其中具備負載平衡器和一組對開發及簡單計算很有用的 VM。您會以命令方式引導整個環境，依序引導逐一命令，直到您具備有效安全的 Linux VM，可透過網際網路從任何地方連線該 VM。然後您就可以繼續進行更複雜的網路和環境。

在過程中，您將了解資源管理員部署模型提供給您的相依性階層，以及它提供多少功能。一旦您了解系統建置的方式，您就可以使用 [Azure Resource Manager 範本](../resource-group-authoring-templates.md)更快地建置系統。一旦您看到環境的組件如何彼此搭配運作，就可以更輕鬆地建立範本來將它們自動化。

環境將會包含︰

- 位於可用性設定組中的兩部 VM
- 連接埠 80 上具有負載平衡規則的負載平衡器
- 避免 VM 產生不必要流量的網路安全性群組規則

![基本環境概觀](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

若要建立此自訂環境，您需要安裝最新的 [Azure CLI](../xplat-cli-install.md) 並處於資源管理員模式 (`azure config mode arm`)。您也需要 JSON 剖析工具 - 這個範例使用 [jq](https://stedolan.github.io/jq/)。

## 快速命令
下列快速命令可用於建置您的自訂環境。如需進一步了解您建置環境時，每個命令執行之動作的概觀，請閱讀[以下的詳細逐步解說步驟](#detailed-walkthrough)。

建立資源群組

```bash
azure group create TestRG -l westeurope
```

使用 JSON 剖析器確認 RG

```bash
azure group show TestRG --json | jq '.'
```

建立儲存體帳戶

```bash
azure storage account create -g TestRG -l westeurope --kind Storage --sku-name GRS computeteststore
```

使用 JSON 剖析器確認儲存體

```bash
azure storage account show -g TestRG computeteststore --json | jq '.'
```

建立虛擬網路

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

建立子網路

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

使用 JSON 剖析器確認 VNet 和子網路

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

建立公用 IP 位址

```bash
azure network public-ip create -g TestRG -n TestLBPIP -l westeurope -d testlb -a static -i 4
```

建立負載平衡器

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

建立負載平衡器的前端 IP 集區，並建立我們公用 IP 的關聯

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

建立負載平衡器的後端 IP 集區

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

建立負載平衡器的 SSH 輸入 NAT 規則

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM2-SSH -p tcp -f 4223 -b 22
```

建立負載平衡器的 Web 輸入 NAT 規則

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

建立負載平衡器健全狀況探查

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "http" -f healthprobe.aspx -i 15 -c 4
```

確認負載平衡器、IP 集區，以及使用 JSON 剖析器的 NAT 規則

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

建立第一個 NIC

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool" \
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
```

建立第二個 NIC

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool" \
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH"
```

使用 JSON 剖析器確認 NIC

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
azure network nic show TestRG LB-NIC2 --json | jq '.'
```

建立 NSG

```bash
azure network nsg create -g TestRG -n TestNSG -l westeurope
```

加入 NSG 的輸入規則

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 22 --access allow -g TestRG -a TestNSG -n SSHRule
azure network nsg rule create --protocol tcp --direction inbound --priority 1001 \
    --destination-port-range 80 --access allow -g TestRG -a TestNSG -n HTTPRule
```

使用 JSON 剖析器確認 NSG 和輸入規則

```bash
azure network nsg show -g TestRG -n TestNSG --json | jq '.'
```

將 NSG 繫結至 NIC

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

建立可用性設定組

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

建立第一個 Linux VM

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
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

建立第二個 Linux VM

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
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

使用 JSON 剖析器確認建置的所有項目

```bash
azure vm show -g TestRG -n TestVM1 --json | jq '.'
azure vm show -g TestRG -n TestVM2 --json | jq '.'
```

將您已建置的環境匯出為範本，以快速重新建立新執行個體：

```bash
azure resource export TestRG
```

## 詳細的逐步解說
這些更詳細的步驟說明您建置環境時每個命令所做的動作，有助於在您接著建置自己的自訂環境以進行開發或實際執行工作負載時，利用這些概念。

## 建立資源群組，並選擇部署位置

Azure 資源群組是邏輯部署實體，包含啟用資源部署之邏輯管理的組態及其他中繼資料。

```bash
azure group create TestRG westeurope
```

輸出

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

在其他案例中，您將需要儲存體帳戶，用於您的 VM 磁碟和任何您想要新增的額外資料磁碟。簡單地說，您一定要在建立資源群組之後立即建立儲存體帳戶。

我們在此使用 `azure storage account create` 命令，傳遞帳戶的位置、將要控制它的資源群組，以及您想要的儲存體支援類型。

```bash
azure storage account create \  
--location westeurope \
--resource-group TestRG \
--kind Storage --sku-name GRS \
computeteststore
```

輸出

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

讓我們使用 [jq](https://stedolan.github.io/jq/) 工具 (您可以使用 **jsawk** 或任何您想要剖析 JSON 的語言程式庫) 連同 `--json` Azure CLI 選項來檢查使用 `azure group show` 命令的資源群組。

```bash
azure group show TestRG --json | jq                                                                                      
```


輸出

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

若要調查使用 CLI 的儲存體帳戶，您必須先使用下列命令的變化，將本文的儲存體帳戶名稱取代為您自己的，以設定帳戶名稱和金鑰。

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show computeteststore --resource-group testrg --json | jq -r '.string')"
```

然後您就能夠輕鬆地檢視您的儲存體資訊︰

```bash
azure storage container list
```

輸出

```bash
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## 建立您的虛擬網路和子網路

您將需要建立您可以在其中安裝 VM 的 Azure 虛擬網路和子網路。

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

輸出

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

同樣地，讓我們看看如何使用 `azure group show` 和 **jq** 的 --json 選項建置資源。我們現在有 `storageAccounts` 資源和 `virtualNetworks` 資源。

```bash
azure group show TestRG --json | jq '.'
```

輸出

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

現在讓我們在要於其中部署 VM 的 `TestVnet` 虛擬網路中建立子網路。我們使用 `azure network vnet subnet create` 命令搭配我們已經建立的資源︰`TestRG` 資源群組、`TestVNet` 虛擬網路，然後我們會新增子網路名稱 `FrontEnd` 和子網路位址首碼 `192.168.1.0/24`，如下所示。

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

輸出

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

因為子網路是以邏輯方式位於虛擬網路內，所以我們將使用稍有不同的命令尋找子網路資訊 -- `azure network vnet show`，但仍使用 **jq** 檢查 JSON 輸出。

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

輸出

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

## 建立您的公用 IP 位址 (PIP)

現在讓我們使用 `azure network public-ip create` 命令建立公用 IP 位址 (PIP)，此 IP 位址將會指派到您的負載平衡器，並可讓您從網際網路連線到 VM。由於預設值是動態位址，所以我們會使用 `-d testsubdomain` 選項在 **.cloudapp.azure.com** 網域中建立具名的 DNS 項目。

```bash
azure network public-ip create -d testsubdomain TestRG TestPIP westeurope
```

輸出

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

這也是最上層資源，因此您可以使用 `azure group show` 查看它。

```bash
azure group show TestRG --json | jq '.'
```

輸出

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

一如往常，您可以使用更完整的 `azure network public-ip show` 命令，調查更多資源的詳細資料，包括子網域完整的網域名稱 (FQDN)。請注意，公用 IP 位址資源以邏輯方式配置，但尚未指派特定位址。為此，您需要我們尚未建立的負載平衡器。

```bash
azure network public-ip show TestRG TestPIP --json | jq '.'
```

輸出

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
建立負載平衡器可讓您將流量分散到多個 VM (例如執行 Web 應用程式時)。它也會執行在進行維護或大量負載時回應使用者要求的多個 VM，以提供您應用程式的備援。

我們會使用下列項目來建立負載平衡器：

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

輸出

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
我們的負載平衡器很空，因此讓我們建立一些 IP 集區。我們想要為負載平衡器建立兩個 IP 集區：一個用於前端，一個用於後端。前端 IP 集區是我們都可以看到的集區，而且我們將在其中指派稍早所建立的 PIP。之後讓 VM 連接的後端集區，以將流量透過負載平衡器流向它們。

首先，讓我們建立前端 IP 集區︰

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

輸出

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

請注意我們如何使用 `--public-ip-name` 參數來傳入我們稍早建立的 TestLBPIP。這會將公用 IP 位址指派給負載平衡器，因此我們可以透過網際網路連接 VM。

接下來，讓我們建立第二個 IP 集區，這次是針對我們的後端流量︰

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

輸出

```bash
info:    Executing command network lb address-pool create
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : TestBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

我們可以利用 `azure network lb show` 檢查負載平衡器的狀態，並檢查 JSON 輸出：

```bash
azure network lb show TestRG TestLB --json | jq '.'
```

輸出

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
若要實際取得流經我們負載平衡器的流量，我們需要建立 NAT 規則，以指定輸入或輸出動作。您可以指定使用中通訊協定，然後視需要將外部連接埠對應到內部連接埠。在我們的環境中，建立一些規則以允許 SSH 透過負載平衡器流向 VM。我們將設定 TCP 連接埠 4222 和 4223 導向 VM 上的 TCP 連接埠 22 (我們會在稍後建立)：

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
```

輸出

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

讓我們繼續進行，並建立 TCP 連接埠 80 的 NAT 規則，以將規則連結到我們的 IP 集區。而不是將規則個別連結到我們的 VM，表示我們只要在 IP 集區中加入或移除 VM，並讓負載平衡器自動調整流量的流程：

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

輸出

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

健全狀況探查會定期檢查受負載平衡器保護的 VM，確定它們為作業中並依定義回應要求。否則，會從作業中移除它們，確保不要將使用者導向到它們。您可以定義健全狀況探查的自訂檢查，以及間隔和逾時值。如需健康狀態探查的詳細資訊，請閱讀[負載平衡器探查](../load-balancer/load-balancer-custom-probe-overview.md)。

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "http" -f healthprobe.aspx -i 15 -c 4
```

輸出

```bash
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : HealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Http
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

在這裡，我們指定 15 秒的健全狀況檢查間隔，而且在負載平衡器將該主機視為不再運作之前，我們最多可以遺漏 4 個探查 (1 分鐘)。

## 確認負載平衡器
完成所有負載平衡器組態。您已建立負載平衡器、建立前端 IP 集區並指派其公用 IP，然後建立 VM 將連接的後端 IP 集區。接下來，您已建立允許 SSH 連接到 VM 以進行管理的 NAT 規則，以及允許 TCP 連接埠 80 用於 Web 應用程式的規則。最後，為了確保使用者不會嘗試存取不再運作和不再提供內容的 VM，您加入健全狀況探查來定期檢查 VM。

讓我們檢閱您負載平衡器現在的樣子︰

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

輸出

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
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/probes/HealthProbe",
      "protocol": "Http",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 4,
      "requestPath": "healthprobe.aspx",
      "provisioningState": "Succeeded",
      "name": "HealthProbe"
    }
  ]
}
```

## 建立 NIC 與 Linux VM 搭配使用

因為您可能會將規則套用至 NIC 的使用上，並有多個規則，即使 NIC 也可以程式設計方式使用。請注意，在下列 `azure network nic create` 命令中，您將我們的 NIC 連結到負載後端 IP 集區，並將我們的 NAT 規則與允許的 SSH 流量建立關聯。若要這麼做，需要指定 Azure 訂用帳戶的訂用帳戶識別碼來取代 `<GUID>`：

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
     -d /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool \
     -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH
```

輸出

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

您可以使用 `azure network nic show` 命令，直接檢查資源來查看詳細資料。

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
```

輸出

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

讓我們繼續進行，並建立第二個 NIC，再次連結到後端 IP 集區，而這次是允許 SSH 流量的第二個 NAT 規則︰

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d  /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool \
    -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH
```

## 建立您的網路安全性群組和規則

我們現在會建立網路安全性群組 (NSG) 和管理 NIC 存取權的輸入規則。

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

> [AZURE.NOTE] 輸入規則是輸入網路連線的篩選器。在此範例中，我們會將 NSG 繫結至 VM 虛擬網路介面卡 (NIC)，這表示任何對連接埠 22 的要求都會在 VM 上傳遞到 NIC。因為這是關於網路連線開啟連接埠的規則 (並非傳統部署中的端點)，所以您必須保留 `--source-port-range` 設為 '*' (預設值) 才能接受來自**任何**要求連接埠的輸入要求，這些要求通常是動態的。

## 繫結至 NIC

將 NSG 繫結至 NIC：

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
```

```bash
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

## 建立可用性設定組
可用性設定組有助於將 VM 分散到容錯網域和升級網域。為 VM 建立可用性設定組：

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

容錯網域定義一個虛擬機器群組，且群組內的虛擬機器會共用通用電源和網路交換器。根據預設，可用性設定組內所設定的虛擬機器最多分散到三個容錯網域。概念是其中一個容錯網域中的硬體問題將不會影響每個執行您應用程式的 VM。將多個 VM 放到一個可用性設定組時，Azure 會自動將它們分散到容錯網域。

升級網域表示虛擬機器群組和可同時重新啟動的基礎實體硬體。重新啟動的升級網域順序可能不會在規劃的維護事件期間循序進行，而只會一次重新啟動一個升級網域。同樣地，將多個 VM 放到一個可用性設定網站內時，Azure 會自動將它們分散到升級網域。

您可以深入了解[管理 VM 的可用性](./virtual-machines-linux-manage-availability.md)

## 建立 Linux VM

您已建立儲存體和網路資源以支援可存取網際網路的 VM。現在讓我們建立 VM，並利用沒有密碼的 SSH 金鑰保障其安全。在此情況下，我們要根據最新的 LTS 建立 Ubuntu VM。我們會使用 `azure vm image list` 找出該映像資訊 (如[尋找 Azure VM 映像](virtual-machines-linux-cli-ps-findimage.md)所述)。我們過往使用命令 `azure vm image list westeurope canonical | grep LTS` 選取映像，而在此情況下，我們將使用 `canonical:UbuntuServer:14.04.4-LTS:14.04.201604060`，但是我們將在最後一個欄位傳遞 `latest`，以便未來可隨時取得最新的組建 (我們使用的字串將會是 `canonical:UbuntuServer:14.04.4-LTS:14.04.201604060`)。

> [AZURE.NOTE] 已使用 **ssh-keygen -t rsa -b 2048** 在 Linux 或 Mac 上建立 ssh rsa 公用和私人金鑰組的任何人，都熟悉下一個步驟。如果您的 `~/.ssh` 目錄中沒有任何憑證金鑰組，您可以建立憑證金鑰組︰<br /> 1. 使用 `azure vm create --generate-ssh-keys` 選項自動進行 2. 使用[自行建立指示](virtual-machines-linux-ssh-from-linux.md)手動進行 <br /> 或者，您可以使用 `azure vm create --admin-username --admin-password` 選項，以使用通常較不安全的使用者名稱和密碼方法，在建立 VM 之後驗證您的 SSH 連線。

我們會藉由使用 `azure vm create` 命令結合所有資源和資訊，以建立 VM。

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
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

輸出

```bash
info:    Executing command vm create
+ Looking up the VM "TestVM1"
info:    Verifying the public key SSH file: /home/ifoulds/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_A1"
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

您立即可以使用預設 SSH 金鑰連接到您的 VM，並確定您指定適當的連接埠，因為我們通過負載平衡器 (針對我們的第一個 VM，我們設定 NAT 規則將連接埠 4222 轉送到我們的 VM)︰

```bash
 ssh ops@testlb.westeurope.cloudapp.azure.com -p 4222
```

輸出

```bash
The authenticity of host '[testlb.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[testlb.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-58-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Wed Apr 27 23:44:06 UTC 2016

  System load: 0.37              Memory usage: 5%   Processes:       81
  Usage of /:  37.3% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

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
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

而您現在可以使用 `azure vm show testrg testvm` 命令來檢查您建立的內容。此時，您在 Azure 中有受負載平衡器保護的執行中 Ubuntu VM，您只能利用您擁有的 SSH 金鑰組登入；密碼會停用。您可以安裝 nginx 或 httpd 並部署 Web 應用程式，然後查看透過負載平衡器流向兩個 VM 的流量。

```bash
azure vm show TestRG TestVM1
```

輸出

```bash
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "LB-NIC1"
data:    Id                              :/subscriptions/8fa5cd83-7fbb-431a-af16-4a20dede8802/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/TestVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :TestVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_A1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli1cca1d20a1dcf56c-os-1461800591317
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://computeteststore.blob.core.windows.net/vhds/cli1cca1d20a1dcf56c-os-1461800591317.vhd
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
data:          MAC Address               :00-0D-3A-20-F8-8B
data:          Provisioning State        :Succeeded
data:          Name                      :LB-NIC1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Compute/availabilitySets/TESTAVAILSET
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://computeteststore.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK
```


## 將環境匯出為範本
您現在已經建立這個環境，如果您想要使用相同參數建立其他開發環境，或想要建立相符的生產環境時該怎麼辦？ Resource Manager 使用 JSON 範本，其中定義了您環境的所有參數，可讓您藉由參考這個 JSON 範本建立整個環境。您可以[手動建立 JSON 範本](../resource-group-authoring-templates.md)，或直接將現有的環境匯出，為您建立 JSON 範本：

```bash
azure group export TestRG
```

這會在您目前的工作目錄中建立 `TestRG.json` 檔案。然後，當您從這個範本建立新環境時，系統會提示您輸入所有的資源名稱，例如負載平衡器、網路介面、VM 等等。您可以將這些資訊填入範本，方法是將 `-p` 或 `--includeParameterDefaultValue` 加入到前述的 `azure group export` 命令中、編輯 JSON 範本來指定資源名稱，或[建立只指定資源名稱的 parameters.json 檔案](../resource-group-authoring-templates.md#parameters)。

若要從範本建立新環境：

```bash
azure group deployment create -f TestRG.json -g NewRGFromTemplate
```

您可以閱讀[從範本部署的更多詳細資料](../resource-group-template-deploy-cli.md)，包括如何以累加方式更新環境、使用參數檔案，和從單一儲存體位置存取範本。

## 後續步驟

現在您已準備好開始處理多個網路元件和 VM。您可以利用此處介紹的核心元件，使用這個相同環境來建置您的應用程式。

<!---HONumber=AcomDC_0622_2016-->