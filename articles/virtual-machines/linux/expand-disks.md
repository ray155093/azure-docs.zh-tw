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
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 4a0b8254ec80576576afde7af34828025d1d2f0a
ms.contentlocale: zh-tw
ms.lasthandoff: 05/11/2017

---

# 如何使用 Azure CLI 擴充 Linux VM 上的虛擬硬碟
<a id="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli" class="xliff"></a>
在 Azure 中，Linux 虛擬機器 (VM) 上作業系統 (OS) 的預設虛擬硬碟大小通常是 30 GB。 您可以[新增資料磁碟](add-disk.md)來提供更多儲存空間，但您可能也想要擴充 OS 磁碟或現有的資料磁碟。 本文將詳細說明如何使用 Azure CLI 2.0 來擴充 Linux VM 的受控磁碟。 您也可以使用 [Azure CLI 1.0](expand-disks-nodejs.md) 來擴充非受控的 OS 磁碟。

## 擴充磁碟
<a id="expand-disk" class="xliff"></a>
請確定您已安裝最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2) 並使用 [az login](/cli/azure/#login) 登入 Azure 帳戶。

在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包含 myResourceGroup 與 myVM。

1. 當 VM 正在執行時，無法對虛擬硬碟執行作業。 使用 [az vm deallocate](/cli/azure/vm#deallocate) 解除配置您的 VM。 下列範例會解除配置名為 myResourceGroup 資源群組中名為 myVM 的 VM：

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `az vm stop` 不會釋放計算資源。 若要釋放計算資源，請使用 `az vm deallocate`。 必須解除配置 VM，才能擴充虛擬硬碟。

2. 使用 [az disk list](/cli/azure/disk#list) 來檢視資源群組中的受控磁碟清單。 下列範例會顯示名為 myResourceGroup 之資源群組中的受控磁碟清單：

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    使用 [az disk update](/cli/azure/disk#update) 擴充所需的磁碟。 下列範例會將名為 myDataDisk 的受控磁碟大小擴充為 200 GB：

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > 當您擴充受控磁碟時，更新的大小會對應至最接近的受控磁碟大小。 如需可用受控磁碟大小和階層的表格，請參閱 [Azure 受控磁碟概觀 - 價格和計費](../../storage/storage-managed-disks-overview.md#pricing-and-billing)。

3. 使用 [az vm create](/cli/azure/vm#start) 啟動 VM。 下列範例會啟動名為 myResourceGroup 資源群組中名為 myVM 的 VM：

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

4. 使用適當的認證以 SSH 登入 VM。 若要確認 OS 磁碟已調整大小，請使用 `df -h`。 下列範例輸出顯示主要磁碟分割 (/dev/sdc1) 現在是 200 GB：

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        194G   52M   193G   1% /datadrive
    ```

## 後續步驟
<a id="next-steps" class="xliff"></a>
如果您需要更多儲存空間，您也可以[將資料磁碟新增至 Linux VM](add-disk.md)。 如需磁碟加密的詳細資訊，請參閱[使用 Azure CLI 將 Linux VM 上的磁碟加密](encrypt-disks.md)。

