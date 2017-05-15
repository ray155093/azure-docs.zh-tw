---
title: "Azure 中 Linux VM 的可用性設定組教學課程 | Microsoft Docs"
description: "了解 Azure 中 Linux VM 的可用性設定組。"
documentationcenter: 
services: virtual-machines-linux
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: d082b37a2e070136178259c54ada8dc141f81e13
ms.contentlocale: zh-tw
ms.lasthandoff: 05/03/2017


---

# <a name="how-to-use-availability-sets"></a>如何使用可用性設定組

在本教學課程中，您將了解如何藉由將虛擬機器 (VM) 放入名為可用性設定組的邏輯群組，來提高它們的可用性。 當您在可用性設定組中建立 VM 時，Azure 平台會將 VM 分散到基礎結構上。 如果發生硬體故障或平台上預定進行的維修作業，使用可用性設定組可確保至少會有一部 VM 仍然維持執行。

您可以使用最新的 [Azure CLI 2.0](/cli/azure/install-azure-cli) 來完成本教學課程中的步驟。

## <a name="availability-set-overview"></a>可用性設定組概觀

您可以在基礎的 Azure 資料中心內，跨硬體邏輯群組建立虛擬機器。 當您建立兩部或多部 VM 時，您的計算和儲存體資源就會分散於這些硬體上，例如伺服器、網路交換器及儲存體。 萬一有硬體元件正在進行維護，此分佈會維護您應用程式的可用性。 可用性設定組可讓您定義這個邏輯群組。

可用性設定組可為 VM 提供高可用性。 此外，您還應該確定您的應用程式設計也容許中斷或維護事件。

## <a name="create-an-availability-set"></a>建立可用性設定組

您可以使用 [az vm availability-set create](/cli/azure/availability-set#create) 來建立可用性設定組。 在此範例中，我們將針對 *myResourceGroupAvailability* 資源群組中名為 *myAvailabilitySet* 的可用性設定組，同時將更新數目和容錯網域數目設為 *2*。

```azurecli
az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

## <a name="create-vms-inside-an-availability-set"></a>建立位於可用性設定組內的 VM

VM 需要建立於可用性設定組內，以確保其會正確地分散到硬體上。 您無法在建立可用性設定組之後，將現有的 VM 加入至其中。 

位置中的硬體已分為多個更新網域和容錯網域。 **更新網域**是一組虛擬機器和可同時重新啟動的基礎實體硬體。 相同**容錯網域**中的 VM 會共用一般儲存體以及通用電源和網路交換器。 

當您使用 [az vm create](/cli/azure/vm#create) 建立 VM 時，會使用 `--availability-set` 參數來指定可用性設定組，以指定可用性設定組的名稱。

```azurecli
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --image Canonical:UbuntuServer:14.04.4-LTS:latest \
     --admin-username azureuser \
     --generate-ssh-keys \
     --no-wait
done 
```

我們現在有 2 部分散於基礎硬體上的虛擬機器。 

## <a name="check-for-available-vm-sizes"></a>檢查可用的 VM 大小 

您稍後可以將更多 VM 加入至可用性設定組，但是需要知道硬體上有哪些可用的 VM 大小。 針對可用性設定組，使用 [az vm availability-set list-sizes](/cli/azure/availability-set#list-sizes) 來列出硬體叢集上所有可用的大小。

```azurecli
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table  
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用可用性設定組。 請前進到下一個教學課程，以了解虛擬機器擴展集。

[建立 VM 擴展集](tutorial-create-vmss.md)


