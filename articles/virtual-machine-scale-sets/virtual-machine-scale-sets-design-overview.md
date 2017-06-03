---
title: "設計 Azure 虛擬機器擴展集進行調整 | Microsoft Docs"
description: "深入了解如何設計 Azure 虛擬機器擴展集進行調整"
keywords: "linux 虛擬機器, 虛擬機器擴展集"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: negat
ms.translationtype: Human Translation
ms.sourcegitcommit: e869b06935736fae72bd3b5407ebab7c3830098d
ms.openlocfilehash: de3687a1bf36bf49db400a5660ac631f20b629d0
ms.contentlocale: zh-tw
ms.lasthandoff: 02/14/2017


---
# <a name="designing-vm-scale-sets-for-scale"></a>設計 VM 擴展集以進行調整
本主題會討論虛擬機器擴展集的設計考量。 如需虛擬機器擴展集的相關資訊，請參閱 [虛擬機器擴展集概觀](virtual-machine-scale-sets-overview.md)。

## <a name="storage"></a>儲存體

### <a name="scale-sets-with-azure-managed-disks"></a>包含 Azure 受控磁碟的擴展集
現在可以使用 [Azure 受控磁碟](../storage/storage-managed-disks-overview.md)建立擴展集。 受控磁碟可提供下列優點：
- 您不必為擴展集 VM 預先建立一組 Azure 儲存體帳戶。
- 您可以為擴展集中的 VM 定義[附加的資料磁碟](virtual-machine-scale-sets-attached-disks.md)。
- 可以設定擴展集以[支援集合中多達 1,000 個 VM](virtual-machine-scale-sets-placement-groups.md)。 

從 "2016-04-30-preview" 版的 Azure 計算 API 開始，您可以建立包含受控磁碟的擴展集。 如需將擴展集範本轉換為受控磁碟的資訊，請參閱[將擴展集範本轉換為受控磁碟擴展集範本](virtual-machine-scale-sets-convert-template-to-md.md)。

### <a name="user-managed-storage"></a>使用者管理的儲存體
未使用 Azure 受控磁碟定義的擴展集依賴使用者建立的儲存體帳戶來儲存擴展集中 VM 的 OS 磁碟。 建議每個儲存體帳戶 20 部 VM 或更小的比率，以期達到最大 IO，而且也可利用_過度佈建_ (如下所示)。 此外，也建議您分散儲存體帳戶名稱的開頭字元英文字母。 這樣做有助於將負載分散到不同的內部系統。 

>[!NOTE]
>VM 擴展集 API 版本 `2016-04-30-preview` 支援使用 Azure 受控磁碟做為作業系統磁碟和任何額外的資料磁碟。 如需詳細資訊，請參閱[受控磁碟概觀](../storage/storage-managed-disks-overview.md)和[使用附加的資料磁碟](virtual-machine-scale-sets-attached-disks.md)。 

## <a name="overprovisioning"></a>過度佈建
從 "2016-03-30" API 版本開始，VM 擴展集預設會「過度佈建」VM。 藉由開啟過度佈建，擴展集實際上所啟動的 VM 數目會比您要求的還多，然後在成功佈建要求的 VM 數目後刪除額外的 VM。 過度佈建可改善佈建成功率並縮短部署時間。 這些額外的 VM 不會計費，也不會計入配額限制。

雖然過度佈建的確改善了佈建的成功率，但也可能導致不是設計來處理額外 VM 出現而後消失的應用程式出現令人困惑的行為。 若要將過度佈建關閉，請確定範本中有下列字串："overprovision": "false"。 您可以在 [VM 擴展集 REST API 文件](https://msdn.microsoft.com/library/azure/mt589035.aspx)中找到更多詳細資料。

如果擴展集使用使用者管理的儲存體，而且您關閉過度佈建，則每個儲存體帳戶可以有 20 部以上的 VM，但基於 IO 效能考量，不建議超過 40 部。 

## <a name="limits"></a>限制
以 Marketplace 映像 (也稱為平台映像) 為建置基礎並設定為使用 Azure 受控磁碟的擴展集可支援多達 1,000 部 VM。 如果您將擴展集設定為支援 100 部以上的 VM，並非所有案例的作用都相同 (適用於負載平衡)。 如需詳細資訊，請參閱[使用大型的虛擬機器擴展集](virtual-machine-scale-sets-placement-groups.md)。 

以使用者管理的儲存體設定的擴展集目前受限於 100 部 VM (且建議此擴展集有 5 個儲存體帳戶)。

若以 Azure 受控磁碟進行設定，以自訂映像 (由您建立的映像) 為建置基礎的擴展集可以有多達 100 部 VM。 如果以使用者管理的儲存體帳戶設定擴展集，它必須在一個儲存體帳戶內建立所有的 OS 磁碟 VHD。 因此，在以自訂映像和使用者管理的儲存體為建置基礎的擴展集中建議 VM 數上限為 20。 如果關閉過度佈建，數目上限為 40。

如果超出這些限制允許的 VM，您必須部署多個擴展集，如 [這個範本](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)中所示。


