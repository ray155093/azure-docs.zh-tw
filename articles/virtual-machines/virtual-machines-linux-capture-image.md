---
title: "使用 Azure CLI 2.0 擷取 Linux VM | Microsoft Docs"
description: "如何使用以 Azure CLI 2.0 建立的受控磁碟，擷取以 Linux 為基礎之 Azure 虛擬機器 (VM) 的映像並將它一般化"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 93abc8b1b14f58b0d0be52517a2b63dfe2dc32fb
ms.openlocfilehash: c72f576d992c9adfa83b9744672397045833c43f
ms.lasthandoff: 02/27/2017


---
# <a name="how-to-generalize-and-capture-a-linux-virtual-machine"></a>如何一般化和擷取 Linux 虛擬機器
若要重複使用在 Azure 中部署和設定的虛擬機器 (VM)，您可擷取 VM 的映像。 此程序也牽涉到將 VM 一般化，以便在從映像部署新 VM 之前，移除個人帳戶資訊。 本文詳細說明如何針對使用 Azure 受控磁碟的 VM，透過 Azure CLI 2.0 擷取 VM 映像。 這些磁碟是由 Azure 平台處理，不需要任何準備或位置來儲存它們。 如需詳細資訊，請參閱 [Azure 受控磁碟概觀](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 本文詳述如何使用 Azure CLI 2.0 來擷取 Linux VM。 您也可以使用 [Azure CLI 1.0](virtual-machines-linux-capture-image-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 來執行這些步驟。

> [!TIP]
> 如果您想要建立一份您現有 Linux VM 的複本，當中包含其特殊的備份或偵錯狀態，請參閱[建立在 Azure 上執行的 Linux 虛擬機器複本](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 如果您想要從內部部署 VM 上傳 Linux VHD，請參閱[上傳自訂磁碟映像並從這個映像建立 Linux VM](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。  


## <a name="before-you-begin"></a>開始之前
請確保符合下列必要條件︰

* **在 Resource Manager 部署模型中建立的 Azure VM** - 如果您尚未建立 Linux VM，可以使用[入口網站](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、[Azure CLI](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或 [Resource Manager 範本](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 視需要設定 VM。 例如，[新增資料磁碟](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、套用更新，並安裝應用程式。 

您還需要安裝最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2)，並使用 [az login](/cli/azure/#login) 來登入 Azure 帳戶。

## <a name="quick-commands"></a>快速命令
如果您需要快速完成工作，下列章節詳細說明在 Azure 中擷取 Linux VM 映像的基本命令。 每個步驟的詳細資訊和內容可在文件其他地方找到，從[這裡](#detailed-steps)開始。 在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包含 `myResourceGroup`、`myVM` 和 `myImage`。

1. 取消佈建來源 VM：

    ```bash
    ssh ops@myvm.westus.cloudapp.azure.com
    sudo waagent -deprovision+user -force
    exit
    ```

2. 使用 [az vm deallocate](/cli/azure/vm#deallocate) 解除配置 VM：

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. 使用 [az vm generalize](/cli/azure/vm#generalize) 將 VM 一般化：
   
    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ```

4. 使用 [az image create](/cli/azure/image#create) 從 VM 資源建立映像：
   
    ```azurecli
    az image create --resource-group myResourceGroup --name myImage --source myVM
    ```

5. 使用 [az vm create](/cli/azure/vm#create) 從映像資源建立 VM：

    ```azurecli
    az vm create --resource-group myResourceGroup --name myVMDeployed --image myImage
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
    ```

## <a name="detailed-steps"></a>詳細步驟
在下列步驟中，您將取消佈建現有的 VM、解除配置及一般化 VM 資源，然後建立映像。 您可以使用此映像，在您訂用帳戶的任何資源群組中建立 VM。 此程序讓 [Azure 受控磁碟](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)的優勢超越非受控磁碟。 使用非受控磁碟，您可建立基礎虛擬硬碟 (VHD) 的 blob 複本，而後受限於在與所複製 VHD blob 相同的儲存體帳戶中建立 VM。 使用受控磁碟，您可建立可跨整個訂用帳戶部署的映像資源。

## <a name="step-1-remove-the-azure-linux-agent"></a>步驟 1：移除 Azure Linux 代理程式
若要讓 VM 可供一般化，請使用 Azure VM 代理程式來取消佈建 VM，以刪除相關檔案和資料。 在目標 Linux VM 上使用 **waagent** 命令搭配 **deprovision** 參數。 如需詳細資訊，請參閱 [Azure Linux 代理程式使用者指南](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

1. 使用 SSH 用戶端連線到 Linux VM。
2. 在 SSH 視窗中，輸入下列命令：
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > 只在您想要擷取作為映像的 VM 上執行這個命令。 這不能保證映像檔中的所有機密資訊都會清除完畢或適合轉散發。
 
3. 輸入 **y** 繼續。 您可以加入 **-force** 參數，便不用進行此確認步驟。
4. 在命令完成之後，請輸入 **exit**。 此步驟會關閉 SSH 用戶端。

## <a name="step-2-capture-the-vm"></a>步驟 2：擷取 VM
使用 Azure CLI 2.0 來一般化和擷取 VM。 在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包含 **myResourceGroup**、**myVnet** 和 **myVM**。

1. 使用 [az vm deallocate](/cli//azure/vm#deallocate) 解除配置已取消佈建的 VM。 下列範例會解除配置 `myResourceGroup` 資源群組中名為 `myVM` 的 VM：
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. 使用 [az vm generalize](/cli//azure/vm#generalize) 將 VM 一般化。 下列範例會將 `myResourceGroup` 資源群組中名為 `myVM` 的 VM 一般化：
   
    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ```

3. 使用 [az image create](/cli//azure/image#create) 建立 VM 資源的映像。 下列範例會使用名為 `myVM` 的 VM 資源，在 `myResourceGroup` 資源群組中建立名為 `myImage` 的映像：
   
    ```azurecli
    az image create --resource-group myResourceGroup --name myImage --source myVM
    ```
   
   > [!NOTE]
   > 此映像與來源 VM 建立於相同的資源群組中。 您可以從此映像，在您訂用帳戶的任何資源群組中建立 VM。 從管理觀點來看，您可能想為您的 VM 資源和映像建立特定的資源群組。

## <a name="step-3-create-a-vm-from-the-captured-image"></a>步驟 3：從擷取的映像建立 VM
使用您以 [az vm create](/cli/azure/vm#create) 建立的映像來建立 VM。 下列範例會從名為 `myImage` 的映像建立名為 `myVMDeployed` 的 VM：

```azurecli
az vm create --resource-group myResourceGroup --name myVMDeployed --image myImage
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
```

使用受控磁碟，您可以從某個映像，在您訂用帳戶的任何資源群組中建立 VM。 這點與使用非受控磁碟有所不同，您只能在與來源 VHD 相同的儲存體帳戶中建立 VM。 若要在與映像不同的資源群組中建立 VM，請指定您映像的完整資源識別碼。 使用 [az image list](/cli/azure/image#list) 來檢視映像清單。 輸出類似於下列範例：

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

下列範例藉由指定映像資源識別碼，進而使用 **az vm create** 在與來源映像不同的資源群組中建立 VM︰

```azurecli
az vm create --resource-group myOtherResourceGroup --name myOtherVMDeployed 
    --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage"
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
```


### <a name="verify-the-deployment"></a>驗證部署
現在使用您建立的虛擬機器的 SSH 來驗證部署並開始使用新的 VM。 若要透過 SSH 連接，請利用 [az vm show](/cli/azure/vm#show) 尋找您 VM 的 IP 位址或 FQDN：

```azurecli
az vm show --resource-group myResourceGroup --name myVM --show-details
```

## <a name="next-steps"></a>後續步驟
您可以從來源 VM 映像建立多個 VM。 如果您需要變更您的映像︰ 

- 從映像建立 VM。
- 進行任何更新或組態變更。
- 再次遵循相關步驟，以取消佈建、解除配置、一般化及建立映像。
- 在日後的部署中使用這個新映像。 如有需要，刪除原始的映像。

如需有關使用 CLI 管理 VM 的詳細資訊，請參閱[Azure CLI 2.0](/cli/azure/overview)。

