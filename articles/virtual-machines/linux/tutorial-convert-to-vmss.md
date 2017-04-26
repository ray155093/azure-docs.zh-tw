---
title: "將 Azure VM 轉換為擴展集 | Microsoft Docs"
description: "使用 Azure CLI 建立及部署 Linux Azure 虛擬機器擴展集。"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/05/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 8d3376d2791b1349298db618d475ce5573083702
ms.lasthandoff: 04/07/2017

---

# <a name="convert-an-existing-azure-virtual-machine-to-a-scale-set"></a>將現有 Azure 虛擬機器轉換為擴展集

本教學課程說明如何使用 Azure CLI 2.0 將虛擬機器轉換為虛擬機器擴展集。 您也會了解如何自動化擴展集中的虛擬機器組態。 如需有關如何安裝 Azure CLI 2.0 的詳細資訊，請參閱[開始使用 Azure CLI 2.0](/cli/azure/get-started-with-azure-cli.md)。 如需調整集的詳細資訊，請參閱 [虛擬機器調整集](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)。

## <a name="step-1---deprovision-the-vm"></a>步驟 1- 取消佈建 VM

使用 SSH 連線到 VM。

使用 Azure VM 代理程式來取消佈建 VM，以刪除相關檔案和資料。 如需取消佈建的詳細概觀，請參閱[擷取 Linux 虛擬機器](capture-image.md)。

```bash
sudo waagent -deprovision+user -force
exit
```

## <a name="step-2---capture-an-image-of-the-vm"></a>步驟 2 - 擷取 VM 的映像

如需擷取的詳細概觀，請參閱[擷取 Linux 虛擬機器](capture-image.md)。

使用 [az vm deallocate](/cli/azure/vm#deallocate) 解除配置 VM：

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

使用 [az vm generalize](/cli/azure/vm#generalize) 將 VM 一般化：

```azurecli
az vm generalize --resource-group myResourceGroup --name myVM
```

使用 [az image create](/cli/azure/image#create) 從 VM 資源建立映像：

```azurecli
az image create --resource-group myResourceGroup --name myImage --source myVM
```

## <a name="step-3---create-the-scale-set"></a>步驟 3 - 建立擴展集

取得映像的 **ID**。

```azurecli
az image show --resource-group myResourceGroup --name myImage --query id
```

```json
"/subscriptions/afbdaf8b-9188-4651-bce1-9115dd57c98b/resourceGroups/vmtest/providers/Microsoft.Compute/images/myImage"
```

使用 [az vmss create](/cli/azure/vmss#create) 從映像資源建立 VM：

```azurecli
az vmss create --resource-group myResourceGroup --name myScaleSet --image /subscriptions/afbdaf8b-9188-4651-bce1-9115dd57c98b/resourceGroups/vmtest/providers/Microsoft.Compute/images/myImage --upgrade-policy-mode automatic --vm-sku Standard_DS1_v2 --data-disk-sizes-gb 10 --admin-username azureuser --generate-ssh-keys
```

此命令也會連結 10 GB 的資料磁碟。 請注意，根據選擇的 VM 大小 (我們使用 **Standard_DS1_v2**)，允許的資料磁碟數目也會不同。 如需詳細資訊，請檢閱[虛擬機器大小](sizes.md)。

當擴展集完成之後，與它連線。 使用 [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info)取得 SSH 之執行個體的 IP 位址清單：

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroup --name myScaleSet
```

```json
[
  "52.183.00.000:50000",
  "52.183.00.000:50003"
]
```

您現在可以連線到虛擬機器執行個體，以初始化資料磁碟

```bash
ssh -i ~/.ssh/id_rsa.pub -p 50000 azureuser@52.183.00.000
```

## <a name="step-4---initialize-the-data-disk"></a>步驟 4 - 初始化資料磁碟

連線到虛擬機器時，使用 `fdisk` 對磁碟進行磁碟分割：

```bash
(echo n; echo p; echo 1; echo  ; echo  ; echo w) | sudo fdisk /dev/sdc
```

使用 `mkfs` 命令將檔案系統寫入至磁碟分割：

```bash
sudo mkfs -t ext4 /dev/sdc1
```

掛接新磁碟，使其可在作業系統中供存取︰

```bash
sudo mkdir /datadrive ; sudo mount /dev/sdc1 /datadrive
```

現在可以透過 datadrive 掛接點存取磁碟，掛接點可以使用 `ls /datadrive/` 來驗證。

結束 SSH 工作階段。


## <a name="step-5---configure-firewall"></a>步驟 5 - 設定防火牆

透過防火牆對擴展集所裝載的 Web 伺服器製造一個漏洞。 擴展集建立時，也會建立負載平衡器，您使用它以 **SSH** 到個別虛擬機器。 若要開啟連接埠，您需要兩項資訊，可以使用 Azure CLI 取得。

* **前端 IP 位址集區**  
`az network lb show --resource-group myResourceGroup --name myScaleSetLB --output table --query frontendIpConfigurations[0].name`

* **後端 IP 位址集區**  
`az network lb show --resource-group myResourceGroup --name myScaleSetLB --output table --query backendAddressPools[0].name`

使用這兩個名稱，您可以開啟連接埠 **80**。

```azurecli
az network lb rule create --backend-pool-name myScaleSetLBBEPool --backend-port 80 --frontend-ip-name loadBalancerFrontEnd --frontend-port 80 --name webserver --protocol tcp --resource-group myResourceGroup --lb-name myScaleSetLB
```


## <a name="step-6---automate-configuration"></a>步驟 6 - 自動化組態

必須在每個虛擬機器執行個體上設定資料磁碟。 我們可以使用 **CustomScript** 擴充功能來自動化虛擬機器的組態。

首先，建立 *.sh* 指令碼，其中包含磁碟格式命令。

```sh
#!/bin/bash

# Setup the data disk
(echo n; echo p; echo 1; echo  ; echo  ; echo w) | fdisk /dev/sdc
fdisk /dev/sdc
mkfs -t ext4 /dev/sdc1
mkdir /datadrive
mount /dev/sdc1 /datadrive

exit 0
```

接下來，將該指令碼檔案上傳到 **CustomScript** 擴充功能可以存取它的位置。 複本可以在[這裡](https://gist.githubusercontent.com/Thraka/ab1d8b78ac4b23722f3d3c1c03ac5df4)取得。

建立名為 **settings.json** 的本機檔案，並且在其中放置下列 JSON 區塊。 `flieUris` 屬性應該設為指令碼檔案上傳的位置。

```json
{
  "fileUris": ["https://gist.githubusercontent.com/Thraka/ab1d8b78ac4b23722f3d3c1c03ac5df4/raw/3ac6e385010ac675e23ce583ce27b1a752f1b482/prep-vmss.sh"],
  "commandToExecute": "bash prep-vmss.sh" 
}
```

使用 **CustomScript** 擴充功能將此命令部署至您的擴展集，參考我們剛剛建立的 **settings.json** 檔案。

```azurecli
az vmss extension set --publisher Microsoft.Azure.Extensions --version 2.0 --name CustomScript --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

此擴充功能會在所有目前執行個體和稍後藉由調整所建立的任何執行個體上自動執行。

## <a name="step-7---configure-autoscale-rules"></a>步驟 7 - 設定自動調整規則

目前，Azure CLI 中無法設定自動調整規則。 使用 [Azure 入口網站](https://portal.azure.com)以設定自動調整。

## <a name="step-8---management-tasks"></a>步驟 8 - 管理工作

在擴展集生命週期中，您可能需要執行一或多個管理工作。 此外，您可能想要建立會自動化各種生命週期工作的指令碼，Azure CLI 提供快速的方式來執行這些工作。 以下是一些常見工作。

### <a name="get-connection-info"></a>取得連線資訊

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroup --name myScaleSet
```

### <a name="set-instance-count-manual-scale"></a>設定執行個體計數 (手動調整)

```azurecli
az vmss scale --resource-group myResourceGroup --name myScaleSet --new-capacity 4
```

### <a name="delete-resource-group"></a>刪除資源群組

刪除資源群組同時會刪除其內含的所有資源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟
若要深入了解本教學課程中介紹的虛擬機器擴展集功能，請參閱下列資訊︰

- [Azure 虛擬機器擴展集概觀](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)
- [Azure 負載平衡器概觀](../../load-balancer/load-balancer-overview.md)
- [使用網路安全性群組來控制網路流量](../../virtual-network/virtual-networks-nsg.md)
