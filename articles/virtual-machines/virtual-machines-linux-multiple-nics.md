---
title: 在 Linux VM 上設定多個 NIC | Microsoft Docs
description: 了解如何使用 Azure CLI 或 Resource Manager 範本，來建立連接多個 NIC 的 VM。
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2016
ms.author: iainfou

---
# 建立具有多個 NIC 的 VM
您可以在 Azure 中，建立連接多個虛擬網路介面 (NIC) 的虛擬機器 (VM)。常見案例是有不同的子網路可用於前端和後端連線，或者專門用來監視或備份解決方案的網路。本文提供快速命令來建立連接多個 NIC 的 VM。如需詳細資訊，包括如何在自己的 Bash 指令碼內建立多個 NIC，請深入了解[部署多個 NIC 的 VM](../virtual-network/virtual-network-deploy-multinic-arm-cli.md)。不同的 [VM 大小](virtual-machines-linux-sizes.md)支援不同數量的 NIC，因此可據以調整您的 VM。

> [!WARNING]
> 當您建立 VM 時，必須連接多個 NIC - 您無法在現有的 VM 中新增 NIC。您可以[根據原始虛擬磁碟建立新的 VM](virtual-machines-linux-copy-vm.md)，並且在部署 VM 時建立多個 NIC。
> 
> 

## 快速命令
確定您已登入 [Azure CLI](../xplat-cli-install.md)，並且使用的是 Resource Manager 模式 (`azure config mode arm`)。

首先，建立資源群組：

```bash
azure group create TestRG --location WestUS
```

建立儲存體帳戶以放置您的 VM：

```bash
azure storage account create teststorage --resource-group TestRG \
    --location WestUS --kind Storage --sku-name PLRS
```

建立要與 VM 連線的虛擬網路：

```bash
azure network vnet create --resource-group TestRG --location WestUS \
    --name TestVNet --address-prefixes 192.168.0.0/16 
```

建立兩個虛擬網路子網路 - 一個用於前端流量，另一個用於後端流量：

```bash
azure network vnet subnet create --resource-group TestRG --vnet-name TestVNet \
    --name FrontEnd --address-prefix 192.168.1.0/24
azure network vnet subnet create --resource-group TestRG --vnet-name TestVNet \
    --name BackEnd --address-prefix 192.168.2.0/24
```

建立兩個 NIC，將一個 NIC 連接到前端子網路，並將另一個 NIC 連接到後端子網路：

```bash
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC1 --subnet-vnet-name TestVNet --subnet-name FrontEnd
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC2 --subnet-vnet-name TestVNet --subnet-name BackEnd
```

最後，建立您的 VM，並連接您先前建立的兩個 NIC：

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location WestUS \
    --os-type linux \
    --nic-names NIC1,NIC2 \
    --vm-size Standard_DS2_v2
    --storage-account-name teststorage \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## 使用 Azure CLI 建立多個 NIC
如果您先前已使用 Azure CLI 建立 VM，應該會很熟悉快速命令。此程序與建立一個 NIC 或多個 NIC 相同。您可以深入了解更多有關[使用 Azure CLI 部署多個 NIC](../virtual-network/virtual-network-deploy-multinic-arm-cli.md) 的詳細資訊，包括撰寫可進行迴圈程序的指令碼來建立所有的 NIC。

下列範例會建立兩個 NIC，以及一個連線到各個子網路的 NIC：

```bash
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC1 --subnet-vnet-name TestVNet --subnet-name FrontEnd
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC2 --subnet-vnet-name TestVNet --subnet-name BackEnd
```

通常您也可以建立[網路安全性群組](../virtual-network/virtual-networks-nsg.md)或[負載平衡器](../load-balancer/load-balancer-overview.md)來協助管理，以及將流量分散到您的 VM。同樣地，命令與使用多個 NIC 時所使用的相同。您所建立的 NIC 會使用 `azure network nic set` 繫結至網路安全性群組或負載平衡器，如下列範例所示：

```bash
azure network nic set --resource-group TestRG --name NIC1 \
    --network-security-group-name TestNSG
```

建立 VM 時，您現在可以指定多個 NIC。不使用 `--nic-name` 提供單一 NIC，而改用 `--nic-names`，並提供以逗號分隔的 NIC 清單。當您選取 VM 大小時也需多加注意。您可以新增至 VM 的 NIC 總數是有限制的。深入了解 [Linux VM 大小](virtual-machines-linux-sizes.md)。下列範例示範如何指定多個 NIC，然後是使用多個 NIC 支援的 VM 大小 (`Standard_DS2_v2`)：

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location WestUS \
    --os-type linux \
    --nic-names NIC1,NIC2 \
    --vm-size Standard_DS2_v2
    --storage-account-name teststorage \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## 使用 Resource Manager 範本建立多個 NIC
Azure Resource Manager 範本會使用宣告式 JSON 檔案來定義您的環境。您可以閱讀 [Azure Resource Manager 概觀](../resource-group-overview.md)。Resource Manager 範本提供一種方式，可在部署期間建立資源的多個執行個體，例如建立多個 NIC。您使用 *copy* 來指定要建立的執行個體數目：

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

深入了解[使用 *copy* 建立多個執行個體](../resource-group-create-multiple.md)。

您也可以使用 `copyIndex()`，然後在資源名稱後面附加一個數字，讓您能夠建立 `NIC1`、`NIC2`，依此類推。以下顯示附加索引值的範例：

```bash
"name": "[concat('NIC-', copyIndex())]", 
```

您可以閱讀[使用 Resource Manager 範本建立多個 NIC](../virtual-network/virtual-network-deploy-multinic-arm-template.md) 的完整範例。

## 後續步驟
嘗試建立具有多個 NIC 的 VM 時，請務必檢閱 [Linux VM 大小](virtual-machines-linux-sizes.md)。注意每個 VM 大小所支援的 NIC 數目上限。

請記住，您無法在現有 VM 中新增其他 NIC，您必須在部署 VM 時建立所有 NIC。小心規劃您的部署，以確定您一開始就會有所有需要的網路連線。

<!---HONumber=AcomDC_0817_2016-->