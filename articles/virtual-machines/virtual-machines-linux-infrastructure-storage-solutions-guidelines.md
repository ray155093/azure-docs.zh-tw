---
title: "儲存體解決方案指導方針 | Microsoft Docs"
description: "了解適合用來在 Azure 基礎結構服務中部署儲存體解決方案的關鍵設計和實作指導方針。"
documentationcenter: 
services: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3c368f07-189b-4520-bbe2-f4080253bbf2
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: ad01dd5585eab70404d476e234b8d2dcee93f2a9


---
# <a name="storage-infrastructure-guidelines"></a>儲存體基礎結構指導方針
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

本文著重於了解達成最佳虛擬機器 (VM) 效能的儲存體需求及設計考量。

## <a name="implementation-guidelines-for-storage"></a>儲存體的實作指導方針
决策：

* 您是否需要針對工作負載使用標準或進階儲存體？
* 您是否需要進行磁碟等量以建立大於 1023 GB 的磁碟？
* 您是否需要進行磁碟等量以獲得工作負載的最佳 I/O 效能？
* 您需要用來裝載 IT 工作負載或基礎結構的是哪種儲存體帳戶組合？

工作：

* 檢閱您正在部署的應用程式 I/O 需求，並規劃適當的儲存體帳戶數目和類型。
* 使用您的命名慣例來建立儲存體帳戶組合。 您可以使用 Azure CLI 或入口網站。

## <a name="storage"></a>儲存體
Azure 儲存體是部署和管理虛擬機器 (VM) 和應用程式的重要部分。 Azure 儲存體提供服務來儲存檔案資料、非結構化資料和訊息，同時也是支援 VM 的基礎結構的一部分。

有兩種可供支援 VM 使用的儲存體帳戶：

* 標準儲存體帳戶讓您能夠存取 blob 儲存體 (用於存放 Azure VM 磁碟)、表格儲存體、佇列儲存體和檔案儲存體。
* [進階儲存體](../storage/storage-premium-storage.md) 帳戶可針對 I/O 密集的工作負載提供高效能且低延遲的磁碟支援 (例如 MongoDB 分區化叢集)。 進階儲存體目前僅支援 Azure VM 磁碟。

Azure 會建立含有一個作業系統磁碟、一個暫存磁碟，以及零或多個選用資料磁碟的 VM。 作業系統磁碟和資料磁碟都是 Azure 頁面 Blob，而暫存磁碟會儲存在本機機器所在的節點上。 請注意，在設計應用程式時，請務必僅將此暫存磁碟用於非持續性資料上，因為 VM 可能會在維護事件期間於主機之間移轉。 任何儲存在暫存磁碟上的資料都會遺失。

持久性和高可用性是由基礎 Azure 儲存體環境所提供，以確保您的資料能在發生非計畫性維護或硬體失敗時受到保護。 設計您的 Azure 儲存體環境時，您可以選擇複寫 VM 儲存體：

* 在指定 Azure 資料中心的本機上
* 在指定區域內跨 Azure 資料中心
* 跨不同區域內且跨 Azure 資料中心。

您可以 [深入了解高可用性的複寫選項](../storage/storage-introduction.md#replication-for-durability-and-high-availability)。

作業系統磁碟和資料磁碟的大小上限為 1023 GB。 Blob 的大小上限為 1024 GB 且必須包含 VHD 檔案的中繼資料 (頁尾) (一個 GB 是 1024<sup>3</sup> 個位元組)。 您可以使用邏輯磁碟區管理員 (LVM) 來超越此限制，方法是共用資料磁碟以向 VM 展現大於 1023GB 的邏輯磁碟區。

設計 Azure 儲存體部署時有幾個延展性的限制，請參閱 [Microsoft Azure 訂用帳戶和服務限制、配額與限制](../azure-subscription-service-limits.md#storage-limits)以取得詳細資料。 另請參閱〈 [Azure 儲存體的延展性與效能目標](../storage/storage-scalability-targets.md)〉。

針對應用程式儲存體，您可以儲存非結構化的物件資料，例如文件、影像、備份、設定資料、記錄檔等等。 使用 Blob 儲存體。 與其讓您的應用程式寫入附加至 VM 的虛擬磁碟，該應用程式可以直接寫入 Azure blob 儲存體。 根據您的可用性需求和成本限制，blob 儲存體也提供[經常性存取與非經常性存取儲存層](../storage/storage-blob-storage-tiers.md)的選項。

## <a name="striped-disks"></a>等量磁碟
除了讓您能夠建立大於 1023 GB 的磁碟，在許多情況下，針對資料磁碟使用等量，可藉由允許多個 blob 備份單一磁碟區的儲存體來增強效能。 從單一邏輯磁碟寫入和讀取資料所需的 I/O 會透過等量速度以平行方式繼續進行。

根據 VM 的大小而定，Azure 會強制限制資料磁碟數量和可用的頻寬量。 如需詳細資訊，請參閱 [虛擬機器的大小](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

如果您針對 Azure 資料磁碟使用磁碟等量，請考量下列指導方針：

* 資料磁碟應一律為最大大小 (1023 GB)。
* 連接 VM 大小所允許的資料磁碟數目上限。
* 使用 LVM。
* 避免使用 Azure 資料磁碟快取選項 (快取原則 = 無)。

如需詳細資訊，請參閱[在 Linux VM 上設定 LVM](virtual-machines-linux-configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="multiple-storage-accounts"></a>多個儲存體帳戶
設計您的 Azure 儲存體環境時，隨著您部署的 VM 數目增加，您可以善用多個儲存體帳戶。 這個方式可協助將 I/O 分散到基礎 Azure 儲存體基礎結構上，以維持 VM 和應用程式的最佳效能。 當您設計要部署的應用程式時，請考慮每個 VM 的 I/O 需求，並使那些 VM 在 Azure 儲存體帳戶之間取得平衡。 請盡量避免只將所有高 I/O 需求的 VM 分配到一個或兩個儲存體帳戶上。

如需不同 Azure 儲存體選項的 I/O 功能以及一些建議最大值的詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](../storage/storage-scalability-targets.md)。

## <a name="next-steps"></a>後續步驟
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]




<!--HONumber=Nov16_HO3-->


