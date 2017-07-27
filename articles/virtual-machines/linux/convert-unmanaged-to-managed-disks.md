---
title: "將 Azure 中的 Linux VM 從非受控磁碟轉換為受控磁碟 | Microsoft Docs"
description: "如何在 Resource Manager 部署模型中使用 Azure CLI 2.0 將 Linux VM 從非受控磁碟轉換為 Azure 受控磁碟"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 06/23/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 37c47061b0774d9670b9a2d304d069d5f683c2d2
ms.contentlocale: zh-tw
ms.lasthandoff: 07/01/2017

---

# <a name="convert-a-linux-vm-from-unmanaged-disks-to-azure-managed-disks"></a>將 Linux VM 從非受控磁碟轉換為 Azure 受控磁碟

如果您現有的 Linux 虛擬機器 (VM) 使用非受控磁碟，您可以將 VM 轉換為使用 [Azure 受控磁碟](../../storage/storage-managed-disks-overview.md)。 此程序會轉換 OS 磁碟和任何附加的資料磁碟。

這篇文章說明如何使用 Azure CLI 來轉換 VM。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli.md)。 

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## <a name="convert-single-instance-vms"></a>轉換單一執行個體 VM
本節說明如何將單一執行個體 Azure VM 從非受控磁碟轉換為受控磁碟。 (如果您的 VM 位於可用性設定組中，請參閱下一節)。您可以使用此程序從進階 (SSD) 非受控磁碟轉換為進階受控磁碟，或從標準 (HDD) 非受控磁碟轉換為標準受控磁碟。

1. 使用 [az vm deallocate](/cli/azure/vm#deallocate) 解除配置 VM。 下列範例會解除配置 `myResourceGroup` 資源群組中名為 `myVM` 的 VM：

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. 使用 [az vm convert](/cli/azure/vm#convert) 將 VM 轉換為受控磁碟。 下列程序會轉換名為 `myVM` 的 VM，包括 OS 磁碟和任何資料磁碟︰

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. 轉換為受控磁碟之後，使用 [az vm start](/cli/azure/vm#start) 啟動 VM。 下列範例會啟動 `myResourceGroup` 資源群組中名為 `myVM` 的 VM。

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>轉換可用性設定組中的 VM

如果您想要轉換為受控磁碟的 VM 位於可用性設定組中，您必須先將此可用性設定組轉換為受控可用性設定組。

轉換可用性設定組之前，必須先解除配置可用性設定組中的所有 VM。 計劃在可用性設定組本身轉換為受控可用性設定組後，立即將所有的 VM 轉換為受控磁碟。 然後，啟動所有 VM 並繼續像平常一樣運作。

1. 使用 [az vm availability-set list](/cli/azure/vm/availability-set#list) 列出可用性設定組中的所有 VM。 下列範例會列出 `myResourceGroup` 資源群組中名為 `myAvailabilitySet` 的可用性設定組中的所有 VM：

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. 使用 [az vm deallocate](/cli/azure/vm#deallocate) 解除配置所有 VM。 下列範例會解除配置 `myResourceGroup` 資源群組中名為 `myVM` 的 VM：

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. 使用 [az vm availability-set convert](/cli/azure/vm/availability-set#convert) 轉換可用性設定組。 下列範例會轉換 `myResourceGroup` 資源群組中名為 `myAvailabilitySet` 的可用性設定組：

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. 使用 [az vm convert](/cli/azure/vm#convert) 將所有 VM 轉換為受控磁碟。 下列程序會轉換名為 `myVM` 的 VM，包括 OS 磁碟和任何資料磁碟︰

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. 轉換為受控磁碟之後，使用 [az vm start](/cli/azure/vm#start) 啟動所有 VM。 下列範例會啟動 `myResourceGroup` 資源群組中名為 `myVM` 的 VM。

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="managed-disks-and-azure-storage-service-encryption"></a>受控磁碟和 Azure 儲存體服務加密
如果非受控磁碟位於曾使用[Azure 儲存體服務加密](../../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)加密的儲存體帳戶，您就無法使用上述步驟將未受控磁碟轉換為受控磁碟。 下列步驟將詳細說明如何複製和使用曾位於已加密儲存體帳戶中的未受控磁碟：

1. 使用 [az storage blob copy start](/cli/azure/storage/blob/copy#start)，將虛擬硬碟 (VHD) 複製到從未針對 Azure 儲存體服務加密啟用的儲存體帳戶。

2. 利用下列其中一種方式使用複製的 VM：

* 建立使用受控磁碟的 VM，並在透過 [az vm create](/cli/azure/vm#create) 建立期間指定該 VHD 檔案

* 使用 [az vm disk attach](/cli/azure/vm/disk#attach)，將複製的 VHD 附加至具有受控磁碟的執行中 VM。

## <a name="next-steps"></a>後續步驟
如需儲存體選項的詳細資訊，請參閱 [Azure 受控磁碟概觀](../../storage/storage-managed-disks-overview.md)。

