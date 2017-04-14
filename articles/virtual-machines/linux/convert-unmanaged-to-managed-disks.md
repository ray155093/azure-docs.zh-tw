---
title: "將 Azure 中的 Linux VM 從非受控磁碟轉換為受控磁碟 | Microsoft Docs"
description: "如何使用 Azure CLI 2.0 將 VM 從非受控磁碟轉換為 Azure 受控磁碟"
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
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8429ee543d8cf838765ff9e8624390746474ee0d
ms.lasthandoff: 04/03/2017

---

# <a name="how-to-convert-a-linux-vm-from-unmanaged-disks-to-azure-managed-disks"></a>如何將 Linux VM 從非受控磁碟轉換為 Azure 受控磁碟

如果 Azure 中有使用儲存體帳戶中非受控磁碟的現有 Linux VM，而且您希望這些 VM 能夠利用受控磁碟，您可以轉換 VM。 此程序會轉換 OS 磁碟和任何附加的資料磁碟。 轉換程序需要重新啟動 VM，因此請在預先存在的維護期間排定 VM 移轉。 移轉程序便無法反轉。 請務必在生產環境中執行移轉之前，藉由移轉測試虛擬機器來測試移轉程序。

> [!IMPORTANT]
> 在轉換期間，您會解除配置 VM。 在轉換後啟動 VM 時，VM 會接收新的 IP 位址。 如果您相依於固定 IP，請使用保留的 IP。

如果非受控磁碟位於使用 (或曾經使用) [Azure 儲存體服務加密 (SSE)](../../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 加密的儲存體帳戶，您就無法將非受控磁碟轉換為受控磁碟。 下列步驟將詳細說明如何轉換位於 (曾經位於) 已加密儲存體帳戶中的非受控磁碟︰

- 使用 [az storage blob copy start](/cli/azure/storage/blob/copy#start)，將虛擬硬碟 (VHD) 複製到從未針對 Azure 儲存體服務加密啟用的儲存體帳戶。
- 建立使用受控磁碟的 VM，並在透過 [az vm create](/cli/azure/vm#create) 建立期間指定該 VHD 檔案，或
- 使用 [az vm disk attach](/cli/azure/vm/disk#attach)，將複製的 VHD 附加至具有受控磁碟的執行中 VM。

## <a name="convert-vm-to-azure-managed-disks"></a>將 VM 轉換為 Azure 受控磁碟
本節說明如何將現有的 Azure VM 從非受控磁碟轉換為受控磁碟。 您可以使用此程序從進階 (SDD) 非受控磁碟轉換為進階受控磁碟，或從標準 (HDD) 非受控磁碟轉換為標準受控磁碟。

> [!IMPORTANT]
> 執行下列程序之後，預設 vhds 容器中會保留單一區塊 blob。 檔案的名稱是 “VMName.xxxxxxx.status”。 請勿刪除此保留的狀態物件。 未來應可解決此問題。

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

## <a name="convert-vm-in-an-availability-set-to-managed-disks"></a>將可用性設定組中的 VM 轉換為受控磁碟

如果您想要轉換為受控磁碟的 VM 位於可用性設定組中，您必須先將此可用性設定組轉換為受控可用性設定組。

轉換可用性設定組之前，必須先解除配置可用性設定組中的所有 VM。 計劃在可用性設定組本身轉換為受控可用性設定組後，立即將所有的 VM 轉換為受控磁碟。 然後，您可以啟動所有 VM 並繼續正常運作。

1. 使用 [az vm availability-set list](/cli/azure/vm/availability-set#list) 列出可用性設定組中的所有 VM。 下列範例會列出 `myResourceGroup` 資源群組中名為 `myAvailabilitySet` 的可用性設定組中的所有 VM：

    ```azurecli
    az vm availability-set show --resource-group myResourceGroup \
        --name myAvailabilitySet --query [virtualMachines[*].id] --output table
    ```

2. 使用 [az vm deallocate](/cli/azure/vm#deallocate) 解除配置所有 VM。 下列範例會解除配置 `myResourceGroup` 資源群組中名為 `myVM` 的 VM：

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. 使用 [az vm availability-set convert](/cli/azure/vm/availability-set#convert) 轉換可用性設定組。 下列範例會轉換 `myResourceGroup` 資源群組中名為 `myAvailabilitySet` 的可用性設定組：

    ```azurecli
    az vm availability-set convert --resource-group myResourceGroup \
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

## <a name="next-steps"></a>後續步驟
如需儲存體選項的詳細資訊，請參閱 [Azure 受控磁碟概觀](../../storage/storage-managed-disks-overview.md)

