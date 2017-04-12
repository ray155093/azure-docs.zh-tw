---
title: "使用 Azure CLI 2.0 建立連接多個 NIC 的 Linux VM | Microsoft Docs"
description: "了解如何使用 Azure CLI 2.0 或 Resource Manager 範本，來建立連結多個 NIC 的 Linux VM。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/10/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 48abd505b0c8c7b5ef6c6fbf58c45275a82b2da3
ms.lasthandoff: 04/03/2017


---
# <a name="create-a-linux-vm-with-multiple-nics"></a>建立連接多個 NIC 的 Linux VM
您可以在 Azure 中，建立連接多個虛擬網路介面 (NIC) 的虛擬機器 (VM)。 常見案例是有不同的子網路可用於前端和後端連線，或者專門用來監視或備份解決方案的網路。 本文提供快速命令來建立連接多個 NIC 的 VM。 如需詳細資訊，包括如何在自己的 Bash 指令碼內建立多個 NIC，請深入了解 [部署多個 NIC 的 VM](../../virtual-network/virtual-network-deploy-multinic-arm-cli.md)。 不同的 [VM 大小](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 支援不同數量的 NIC，因此可據以調整您的 VM。

本文詳述如何使用 Azure CLI 2.0 建立具有多個 NIC 的 VM。 您也可以使用 [Azure CLI 1.0](multiple-nics-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 來執行這些步驟。

> [!WARNING]
> 當您建立 VM 時，必須連接多個 NIC - 您無法在現有的 VM 中新增 NIC。 您可以[根據原始虛擬磁碟建立 VM](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)，並且在部署 VM 時建立多個 NIC。


## <a name="create-supporting-resources"></a>建立支援資源
請安裝最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2) 並使用 [az login](/cli/azure/#login) 來登入 Azure 帳戶。

在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包含 `myResourceGroup`、`mystorageaccount` 和 `myVM`。

首先，使用 [az group create](/cli/azure/group#create) 建立資源群組。 下列範例會在 `WestUS` 位置建立名為 `myResourceGroup` 的資源群組：

```azurecli
az group create --name myResourceGroup --location westus
```

使用 [az network vnet create](/cli/azure/network/vnet#create) 建立虛擬網路。 下列範例會建立名為 `myVnet` 的虛擬網路和名為 `mySubnetFrontEnd` 的子網路：

```azurecli
az network vnet create --resource-group myResourceGroup --name myVnet \
  --address-prefix 192.168.0.0/16 --subnet-name mySubnetFrontEnd --subnet-prefix 192.168.1.0/24
```

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#create) 建立後端流量的子網路。 下列範例會建立名為 `mySubnetBackEnd`的子網路：

```azurecli
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVnet \
    --name mySubnetBackEnd --address-prefix 192.168.2.0/24
```

## <a name="create-and-configure-multiple-nics"></a>建立及設定多個 NIC
您可以深入了解更多有關 [使用 Azure CLI 部署多個 NIC](../../virtual-network/virtual-network-deploy-multinic-arm-cli.md)的詳細資訊，包括撰寫可進行迴圈程序的指令碼來建立所有的 NIC。

您通常會建立[網路安全性群組](../../virtual-network/virtual-networks-nsg.md)或[負載平衡器](../../load-balancer/load-balancer-overview.md)來協助管理，以及將流量分散於您的 VM。 使用 [az network nsg create](/cli/azure/network/nsg#create) 建立網路安全性群組。 下列範例會建立名為 `myNetworkSecurityGroup` 的網路安全性群組：

```azurecli
az network nsg create --resource-group myResourceGroup \
  --name myNetworkSecurityGroup
```

使用 [az network nic create](/cli/azure/network/nic#create) 建立兩個 NIC。 下列範例會建立兩個連接網路安全性群組的 NIC (名為 `myNic1` 和 `myNic2`)，以及一個連接到各個子網路的 NIC：

```azurecli
az network nic create --resource-group myResourceGroup --name myNic1 \
  --vnet-name myVnet --subnet mySubnetFrontEnd \
  --network-security-group myNetworkSecurityGroup
az network nic create --resource-group myResourceGroup --name myNic2 \
  --vnet-name myVnet --subnet mySubnetBackEnd \
  --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>建立 VM 並附加 NIC
當您建立 VM 時，指定您使用 `--nics` 建立的 NIC。 當您選取 VM 大小時也需多加注意。 您可以新增至 VM 的 NIC 總數是有限制的。 深入了解 [Linux VM 大小](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 

使用 [az vm create](/cli/azure/vm#create) 建立 VM。 下列範例使用 [Azure 受控磁碟](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)建立名為 `myVM` 的 VM：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic1 myNic2
```

## <a name="create-multiple-nics-using-resource-manager-templates"></a>使用 Resource Manager 範本建立多個 NIC
Azure Resource Manager 範本會使用宣告式 JSON 檔案來定義您的環境。 您可以閱讀 [Azure Resource Manager 概觀](../../azure-resource-manager/resource-group-overview.md)。 Resource Manager 範本提供一種方式，可在部署期間建立資源的多個執行個體，例如建立多個 NIC。 您使用 *copy* 來指定要建立的執行個體數目：

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

深入了解[使用 *copy* 建立多個執行個體](../../resource-group-create-multiple.md)。 

您也可以使用 `copyIndex()`，然後在資源名稱後面附加一個數字，讓您能夠建立 `myNic1`、`myNic2`，依此類推。以下顯示附加索引值的範例：

```json
"name": "[concat('myNic', copyIndex())]", 
```

您可以閱讀 [使用 Resource Manager 範本建立多個 NIC](../../virtual-network/virtual-network-deploy-multinic-arm-template.md)的完整範例。

## <a name="next-steps"></a>後續步驟
嘗試建立具有多個 NIC 的 VM 時，請務必檢閱 [Linux VM 大小](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 。 注意每個 VM 大小所支援的 NIC 數目上限。 

請記住，您無法在現有 VM 中新增其他 NIC，您必須在部署 VM 時建立所有 NIC。 小心規劃您的部署，以確定您一開始就會有所有需要的網路連線。


