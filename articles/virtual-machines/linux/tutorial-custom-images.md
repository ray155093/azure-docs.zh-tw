---
title: "使用 Azure CLI 建立自訂的 VM 映像 | Microsoft Docs"
description: "教學課程 - 使用 Azure CLI 建立自訂的 VM 映像。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 2ce92b3f0a21f80eb4294161d6d3a5275c992600
ms.contentlocale: zh-tw
ms.lasthandoff: 05/03/2017

---

# <a name="create-a-custom-image-of-an-azure-vm-using-the-cli"></a>使用 CLI 建立 Azure VM 的自訂映像

在本教學課程中，您將了解如何定義自己的 Azure 虛擬機器自訂映像。 自訂映像可讓您使用已設定好的映像建立 VM。 自訂映像可用於啟動預先載入的二進位檔和應用程式、應用程式組態、VM 資料磁碟定義和其他 OS 組態。 建立自訂映像時，您自訂的 VM 以及所有連結的磁碟均會包含於映像中。

您可以使用最新的 [Azure CLI 2.0](/cli/azure/install-azure-cli) 來完成本教學課程中的步驟。

## <a name="before-you-begin"></a>開始之前

下列步驟將詳細說明如何將現有 VM 轉換成可重複使用的自訂映像，以便讓您用來建立新的 VM 執行個體。

若要完成本教學課程中的範例，您目前必須具有虛擬機器。 如有需要，這個[指令碼範例](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md)可以為您建立一部虛擬機器。 逐步完成教學課程之後，請視需要取代資源群組和 VM 名稱。

## <a name="prepare-vm"></a>準備 VM

若要建立虛擬機器的映像，您需要藉由取消佈建、解除配置，然後將來源 VM 標示為一般化，來準備 VM。

### <a name="deprovision-the-vm"></a>取消佈建 VM 

取消佈建會藉由移除電腦特定的資訊來將 VM 一般化。 這個一般化讓您能夠從單一映像部署多部 VM。 解除佈建期間，將主機名稱重設為 localhost.localdomain。 SSH 主機金鑰、名稱伺服器設定、根密碼及快取的 DHCP 租用也會一併刪除。

若要取消佈建 VM，請使用 Azure VM 代理程式 (waagent)。 Azure VM 代理程式會安裝於 VM 上，並管理佈建以及與 Azure 網狀架構控制器進行互動。 如需詳細資訊，請參閱 [Azure Linux 代理程式使用者指南](agent-user-guide.md)。

使用 SSH 連接到您的 VM，並執行命令以取消佈建 VM。 利用 `+user` 引數，同時刪除最後佈建的使用者帳戶及所有相關聯的資料。 以 VM 的公用 IP 位址取代範例 IP 位址。

透過 SSH 連接到 VM。
```bash
ssh azureuser@52.174.34.95
```
取消佈建 VM。

```bash
sudo waagent -deprovision+user -force
```
關閉 SSH 工作階段。

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>解除配置並將 VM 標示為一般化

若要建立映像，必須解除配置 VM。 使用 [az vm deallocate](/cli//azure/vm#deallocate) 解除配置 VM。 
   
```azurecli
az vm deallocate --resource-group myRGCaptureImage --name myVM
```

最後，使用 [az vm generalize](/cli//azure/vm#generalize) 將 VM 的狀態設為一般化，如此一來，Azure 平台就會知道 VM 已一般化。 您只能從一般化的 VM 建立映像。
   
```azurecli
az vm generalize --resource-group myResourceGroupImages --name myVM
```

## <a name="create-the-image"></a>建立映像

現在，您可以使用 [az image create](/cli//azure/image#create) 來建立 VM 的映像。 下列範例會從名為 myVM 的 VM 建立名為 myImage 的映像。
   
```azurecli
az image create \
    --resource-group myResourceGroupImages \
    --name myImage \
    --source myVM
```
 
## <a name="create-vms-from-the-image"></a>從映像建立 VM

現在您已有映像，您可以使用 [az vm create](/cli/azure/vm#create) 從映像建立一個或多個新的 VM。 下列範例會從名為 myImage 的映像建立名為 myVMfromImage 的 VM。

```azurecli
az vm create \
    --resource-group myResourceGroupImages \
    --name myVMfromImage \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何建立自訂的 VM 映像。 請前進到下一個教學課程，以了解如何使虛擬機器具備高可用性。

[建立高可用性 VM](tutorial-availability-sets.md)。


