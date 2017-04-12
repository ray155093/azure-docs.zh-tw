---
title: "在 Azure 中擴充 Linux VM 上的虛擬硬碟 | Microsoft Docs"
description: "了解如何使用 Azure CLI 2.0 擴充 Linux VM 上的虛擬硬碟"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/22/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 07d4afc5f3eedbdcd4686a64acccf40278f139c4
ms.lasthandoff: 04/03/2017

---

# <a name="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli-20"></a>如何使用 Azure CLI 2.0 擴充 Linux VM 上的虛擬硬碟
在 Azure 中，Linux 虛擬機器 (VM) 上作業系統 (OS) 的預設虛擬硬碟大小通常是 30 GB。 您可以[新增資料磁碟](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)來提供更多儲存空間，但您可能也想要擴充 OS 磁碟或現有的資料磁碟。 本文將詳細說明如何使用 Azure CLI 2.0 來擴充 Linux VM 的受控磁碟。 您也可以使用 [Azure CLI 1.0](expand-disks-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 來擴充非受控的 OS 磁碟。

## <a name="expand-managed-disk"></a>擴充受控磁碟
請確定您已安裝最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2) 並使用 [az login](/cli/azure/#login) 登入 Azure 帳戶。

在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包括 `myResourceGroup` 和 `myVM`。

1. 當 VM 正在執行時，無法對虛擬硬碟執行作業。 使用 [az vm deallocate](/cli/azure/vm#deallocate) 解除配置您的 VM。 下列範例會解除配置 `myResourceGroup` 資源群組中名為 `myVM` 的 VM：

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `az vm stop` 不會釋放計算資源。 若要釋放計算資源，請使用 `az vm deallocate`。 必須解除配置 VM，才能擴充虛擬硬碟。

2. 使用 [az disk list](/cli/azure/disk#list) 來檢視資源群組中的受控磁碟清單。 下列範例會顯示名為 `myResourceGroup` 之資源群組中的受控磁碟清單：

    ```azurecli
    az disk list -g myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    使用 [az disk update](/cli/azure/disk#update) 擴充所需的磁碟。 下列範例會將名為 `myDataDisk` 的受控磁碟大小擴充為 `200` GB：

    ```azurecli
    az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 200
    ```

    > [!NOTE]
    > 當您擴充受控磁碟時，更新的大小會對應至最接近的受控磁碟大小。 如需可用受控磁碟大小和階層的表格，請參閱 [Azure 受控磁碟概觀 - 價格和計費](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#pricing-and-billing)。

3. 使用 [az vm create](/cli/azure/vm#start) 啟動 VM。 下列範例會啟動 `myResourceGroup` 資源群組中名為 `myVM` 的 VM：

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

4. 使用適當的認證以 SSH 登入 VM。 若要確認 OS 磁碟已調整大小，請使用 `df -h`。 下列範例輸出會顯示主要磁碟分割 (`/dev/sda1`) 現在是 200 GB：

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        194G   52M   193G   1% /datadrive
    ```

## <a name="next-steps"></a>後續步驟
如果您需要更多儲存空間，您也可以[將資料磁碟新增至 Linux VM](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 如需磁碟加密的詳細資訊，請參閱[使用 Azure CLI 將 Linux VM 上的磁碟加密](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

