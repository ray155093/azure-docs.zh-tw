---
title: "Azure 虛擬網路和 Linux 虛擬機器 | Microsoft Docs"
description: "教學課程 - 使用 Azure CLI 來管理 Azure 虛擬網路和 Linux 虛擬機器"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: de7e77b7d4c26b08e73036b8da67489823100f4c
ms.contentlocale: zh-tw
ms.lasthandoff: 06/20/2017

---

# <a name="manage-azure-virtual-networks-and-linux-virtual-machines-with-the-azure-cli"></a>使用 Azure CLI 來管理 Azure 虛擬網路和 Linux 虛擬機器

Azure 虛擬機器會使用 Azure 網路進行內部和外部的網路通訊。 本教學課程會逐步部署兩部虛擬機器 (VM)，並設定這兩部 VM 的 Azure 網路功能。 本教學課程中的範例假設 VM 已裝載 Web 應用程式與資料庫後端，不過應用程式的部署不在本教學課程範圍中。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 部署虛擬網路
> * 在虛擬網路中建立子網路
> * 將虛擬機器連結至子網路
> * 管理虛擬機器的公用 IP 位址
> * 保護傳入的網際網路流量
> * 保護 VM 至 VM 的流量


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="vm-networking-overview"></a>VM 網路概觀

Azure 虛擬網路可以讓虛擬機器、網際網路與其他 Azure 服務 (例如 Azure SQL database) 之間的網路連線更安全。 虛擬網路會被分割成邏輯區段，稱為子網路。 子網路是用來控制網路流量，並作為安全性界限。 在部署 VM 時，通常會包含連結至子網路的虛擬網路介面。

## <a name="deploy-virtual-network"></a>部署虛擬網路

在本教學課程中，會建立一個具有兩個子網路的虛擬網路。 一個是裝載 Web 應用程式的前端子網路，一個是裝載資料庫伺服器的後端子網路。

建立虛擬網路前，請先使用 [az group create](/cli/azure/group#create) 建立資源群組。 下列範例會在 eastus 建立名為 myRGNetwork 的資源群組。

```azurecli-interactive 
az group create --name myRGNetwork --location eastus
```

### <a name="create-virtual-network"></a>建立虛擬網路

使用 [az network vnet create](/cli/azure/network/vnet#create) 命令建立虛擬網路。 在此範例中，將虛擬網路命名為mvVnet，並指定位址首碼 10.0.0.0/16 給它。 也會建立名為 mySubnetFrontEnd 且首碼為 10.0.1.0/24 的子網路。 本教學課程稍後，會將前端 VM 連線到此子網路。 

```azurecli-interactive 
az network vnet create \
  --resource-group myRGNetwork \
  --name myVnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name mySubnetFrontEnd \
  --subnet-prefix 10.0.1.0/24
```

### <a name="create-subnet"></a>建立子網路

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#create) 命令將新的子網路新增至虛擬網路。 在此範例中，將子網路命名為mySubnetBackEnd，並指定位址首碼 10.0.2.0/24 給它。 所有的後端服務都會使用此子網路。

```azurecli-interactive 
az network vnet subnet create \
  --resource-group myRGNetwork \
  --vnet-name myVnet \
  --name mySubnetBackEnd \
  --address-prefix 10.0.2.0/24
```

此時，已建立一個網路並分割成兩個子網路，一個用於前端的服務，另一個用於後端服務。 在下一節會建立虛擬機器，並將其連線到這些子網路。

## <a name="understand-public-ip-address"></a>認識公用 IP 位址

公用 IP 位址讓您能夠存取網際網路上的 Azure 資源。 本節的教學課程會建立 VM 來示範如何使用公用 IP 位址。

### <a name="allocation-method"></a>配置方法

公用 IP 位址可以動態或靜態配置。 預設是以動態方式配置公用 IP 位址。 VM 解除配置時，就會釋放動態 IP 位址。 在任何包括 VM 解除配置的作業期間，這個行為會導致 IP 位址變更。

可將配置方法設定為靜態，這可確保即使在已取消配置狀態下，IP 位址仍指派給 VM。 使用靜態配置的 IP 位址，則無法指定 IP 位址本身， 而是從可用位址集區配置一個給它。

### <a name="dynamic-allocation"></a>動態配置

使用 [az vm create](/cli/azure/vm#create) 命令建立 VM 時，預設的公用 IP 位址配置方法為動態。 在下列範例中，會建立具有動態 IP 位址的 VM。 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myFrontEndVM \
  --vnet-name myVnet \
  --subnet mySubnetFrontEnd \
  --nsg myNSGFrontEnd \
  --public-ip-address myFrontEndIP \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="static-allocation"></a>靜態配置

使用 [az vm create](/cli/azure/vm#create)命令建立虛擬機器時，需包括 `--public-ip-address-allocation static` 引數來指派靜態公用 IP 位址。 這項作業不會示範在本教學課程中，不過在下一節會將動態配置的 IP 位址變更為靜態配置的位址。 

### <a name="change-allocation-method"></a>變更配置方法

使用 [az network public-ip update](/cli/azure/network/public-ip#update) 命令可以變更 IP 位址的配置方法。 在此範例中，會將前端 VM 的 IP 位址配置方法變更為靜態。

首先，解除配置 VM。

```azurecli-interactive 
az vm deallocate --resource-group myRGNetwork --name myFrontEndVM
```

使用 [az network public-ip update](/azure/network/public-ip#update) 命令更新配置方式。 在此，將 `--allocaion-metod` 設為 static。

```azurecli-interactive 
az network public-ip update --resource-group myRGNetwork --name myFrontEndIP --allocation-method static
```

啟動 VM。

```azurecli-interactive 
az vm start --resource-group myRGNetwork --name myFrontEndVM --no-wait
```

### <a name="no-public-ip-address"></a>無公用 IP 位址

通常，VM 不需要透過網際網路存取。 若要建立無公用 IP 位址的 VM，請使用 `--public-ip-address ""` 引數加上以雙引號括住的空集合。 此組態稍後會在本教學課程中示範。

## <a name="secure-network-traffic"></a>保護網路流量

網路安全性群組 (NSG) 包含安全性規則的清單，可允許或拒絕已連線至 Azure 虛擬網路 (VNet) 之資源的網路流量。 NSG 可與子網路或個別網路介面建立關聯。 當 NSG 與網路介面相關聯時，只會套用相關聯的 VM。 當 NSG 與子網路相關聯時，系統會將規則套用至已連線至子網路的所有資源。 

### <a name="network-security-group-rules"></a>網路安全性群組規則

NSG 規則定義允許或拒絕流量的網路連接埠。 規則可以包含來源和目的地 IP 位址範圍，以便控制特定系統或子網路之間的流量。 NSG 規則也包含優先順序 (介於 1 和 4096)。 系統會依照優先順序評估規則。 優先順序 100 的規則會比優先順序 200 的規則優先評估。

所有 NSG 都包含一組預設規則。 預設規則無法刪除，但因為其會指派為最低優先權，因此可以由您所建立的規則覆寫預設規則。

- **虛擬網路** - 虛擬網路中的流量起始和結束同時允許輸入和輸出方向。
- **網際網路** - 允許輸出流量，但會封鎖輸入流量。
- **負載平衡器** - 允許 Azure 的負載平衡器探查 VM 和角色執行個體的健康狀態。 如果您不使用負載平衡的集合，則可以覆寫此規則。

### <a name="create-network-security-groups"></a>建立網路安全性群組

使用 [az vm create](/cli/azure/vm#create) 命令建立 VM 時，可以同時建立網路安全性群組。 這麼做時，NSG 是與 VM 網路介面相關聯，並會自動建立 NSG 規則以允許從任何來源到連接埠 22 的流量。 稍早在本教學課程中，前端 NSG 已自動和前端 VM 一起建立。 也會自動建立連接埠 22 的 NSG 規則。 

在某些情況下，預先建立 NSG 可能較有幫助，例如不應建立預設 SSH 規則時，或當 NSG 應該連結至子網路時。 

使用 [az network nsg create](/cli/azure/network/nsg#create) 命令建立網路安全性群組。

```azurecli-interactive 
az network nsg create --resource-group myRGNetwork --name myNSGBackEnd
```

它不會將 NSG 關聯至網路介面，而是關聯至子網路。 在此組態中，任何連結至子網路的 VM 都會繼承此 NSG 規則。

以新的 NSG 更新名為mySubnetBackEnd 的現有子網路。

```azurecli-interactive 
az network vnet subnet update \
  --resource-group myRGNetwork \
  --vnet-name myVnet \
  --name mySubnetBackEnd \
  --network-security-group myNSGBackEnd
```

現在，建立連結至mySubnetBackEnd 的虛擬機器。 請注意，`--nsg` 引數的值為一對空的雙引號。 NSG 不需要和 VM 一起建立。 VM 連結到後端子網路，後者以預先建立的後端 NSG 的保護。 這個 NSG 會套用至 VM。 也請注意，`--public-ip-address` 引數的值為一對空的雙引號。 此組態會建立無公用 IP 位址的 VM。 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myBackEndVM \
  --vnet-name myVnet \
  --subnet mySubnetBackEnd \
  --public-ip-address "" \
  --nsg "" \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="secure-incoming-traffic"></a>保護傳入的流量

建立前端 VM 時，已建立 NSG 規則以允許連接埠 22 上的傳入流量。 此規則允許連到 VM 的 SSH 連線。 在此範例中，應該也允許連接埠 80 上的流量。 此組態讓 VM 上的 Web 應用程式可被存取。

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#create) 命令建立連接埠 80 的規則。

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myNSGFrontEnd \
  --name http \
  --access allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 80
```

現在，只可以在連接埠 22和連接埠 80 上存取前端 VM。 所有其他的傳入流量會在網路安全性群組遭到封鎖。 以視覺化方式檢視 NSG 規則組態可能有幫助。 以 [az network rule list](/cli/azure/network/nsg/rule#list) 命令傳回 NSG 規則組態。 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myNSGFrontEnd --output table
```

輸出：

```azurecli-interactive 
Access    DestinationAddressPrefix      DestinationPortRange  Direction    Name                 Priority  Protocol    ProvisioningState    ResourceGroup    SourceAddressPrefix    SourcePortRange
--------  --------------------------  ----------------------  -----------  -----------------  ----------  ----------  -------------------  ---------------  ---------------------  -----------------
Allow     *                                               22  Inbound      default-allow-ssh        1000  Tcp         Succeeded            myRGNetwork      *                      *
Allow     *                                               80  Inbound      http                      200  Tcp         Succeeded            myRGNetwork      *                      *
```

### <a name="secure-vm-to-vm-traffic"></a>保護 VM 至 VM 的流量

網路安全性群組規則也可以套用在 VM 之間。 在此範例中，前端 VM 需要與連接埠 22 和 3306上的後端 VM 通訊。 此組態允許來自前端 VM 的 SSH 連線，也允許前端 VM 上的 應用程式與後端 MySQL 資料庫通訊。 前端和後端虛擬機器之間的所有其他流量應該會被封鎖。

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#create) 命令建立連接埠 22 的規則。 請注意，`--source-address-prefix` 引數指定 10.0.1.0/24 值。 此組態可確保透過 NSG 只允許來自前端子網路的流量。

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myNSGBackEnd \
  --name SSH \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "22"
```

現在新增連接埠 3306 上 MySQL 流量的規則。

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myNSGBackEnd \
  --name MySQL \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "3306"
```

最後，由於 NSG 的預設規則允許相同 VNet 中 VM 之間的所有流量，可以建立一條後端 NSG 規則來封鎖所有流量。 請注意，指定的 `--priority` 值為 300，會降低 NSG 和 MySQL 規則的優先順序。 此組態可確保透過 NSG 會允許 SSH 和 MySQL 流量。

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myNSGBackEnd \
  --name denyAll \
  --access Deny \
  --protocol Tcp \
  --direction Inbound \
  --priority 300 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "*"
```

現在，只可以從前端子網路在連接埠 22 和連接埠 3306 上存取後端 VM。 所有其他的傳入流量會在網路安全性群組遭到封鎖。 以視覺化方式檢視 NSG 規則組態可能有幫助。 以 [az network rule list](/cli/azure/network/nsg/rule#list) 命令傳回 NSG 規則組態。 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myNSGBackEnd --output table
```

輸出：

```azurecli-interactive 
Access    DestinationAddressPrefix    DestinationPortRange    Direction    Name       Priority  Protocol    ProvisioningState    ResourceGroup    SourceAddressPrefix    SourcePortRange
--------  --------------------------  ----------------------  -----------  -------  ----------  ----------  -------------------  ---------------  ---------------------  -----------------
Allow     *                           22                      Inbound      SSH             100  Tcp         Succeeded            myRGNetwork      10.0.1.0/24            *
Allow     *                           3306                    Inbound      MySQL           200  Tcp         Succeeded            myRGNetwork      10.0.1.0/24            *
Deny      *                           *                       Inbound      denyAll         300  Tcp         Succeeded            myRGNetwork      *                      *
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立並保護與虛擬機器相關的 Azure 網路。 您已了解如何︰

> [!div class="checklist"]
> * 部署虛擬網路
> * 在虛擬網路中建立子網路
> * 將虛擬機器連結至子網路
> * 管理虛擬機器的公用 IP 位址
> * 保護傳入的網際網路流量
> * 保護 VM 至 VM 的流量

請前進到下一個教學課程，了解如何使用 Azure 備份保護虛擬機器上的資料。 

> [!div class="nextstepaction"]
> [備份 Azure 中的 Linux 虛擬機器](./tutorial-backup-vms.md)

