---
title: "使用 Azure CLI 建立 Azure VM 擴展集 | Microsoft Docs"
description: "了解 VM 擴展集。"
keywords: "linux 虛擬機器, 虛擬機器擴展集"
services: virtual-machines-linux
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba1aedb6-49cb-4546-8b8b-da97aba8e42d
ms.service: virtual-machine-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/24/2016
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: 5b8950619da3f42bc3c92443a7b9e9f2a97067f9
ms.openlocfilehash: 8d274127a4e1947e42b5544c3c1f62c4e48dcee9


---
# <a name="what-are-virtual-machine-scale-sets"></a>什麼是虛擬機器擴展集？
虛擬機器擴展集可讓您以一個集合來管理多部 VM。 概括而言，擴展集具有下列優缺點︰

優點：

1. 高可用性。 每個擴展集都會將它的 VM 放入具有 5 個容錯網域 (FD) 和 5 個更新網域 (UD) 的可用性設定組，以確保可用性 (如需 FD 和 UD 的詳細資訊，請參閱 [VM 可用性](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json))。 
2. 與 Azure 負載平衡器和應用程式閘道輕鬆整合。
3. 與 Azure 自動調整輕鬆整合。
4. 簡化 VM 的部署、管理和清除。
5. 支援常見 Windows 和 Linux 類別以及自訂映像。

缺點：

1. 無法將資料磁碟連接至擴展集中的 VM 執行個體。 相反地，必須使用 Blob 儲存體、Azure 檔案、Azure 資料表或其他儲存體解決方案。

## <a name="quick-create-using-azure-cli"></a>使用 Azure CLI 快速建立
[!INCLUDE [cli-vmss-quick-create](../../includes/virtual-machines-linux-cli-vmss-quick-create-include.md)]

## <a name="next-steps"></a>後續步驟
如需一般資訊，請參閱 [擴展集的主要登陸頁面](https://azure.microsoft.com/services/virtual-machine-scale-sets/)。

如需文件，請參閱 [擴展集的主要文件頁面](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)。

如需使用擴展集的範例 Resource Manager 範本，請在 [Azure 快速入門範本 github 儲存機制](https://github.com/Azure/azure-quickstart-templates)中搜尋 "vmss"。




<!--HONumber=Jan17_HO4-->


