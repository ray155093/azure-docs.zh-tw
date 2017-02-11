---
title: "發生影響 Azure 雲端服務的 Azure 服務中斷事件時該怎麼辦 | Microsoft Docs"
description: "了解發生影響 Azure 雲端服務的 Azure 服務中斷事件時該怎麼辦。"
services: cloud-services
documentationcenter: 
author: kmouss
manager: drewm
editor: 
ms.assetid: e52634ab-003d-4f1e-85fa-794f6cd12ce4
ms.service: cloud-services
ms.workload: cloud-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: kmouss;aglick
translationtype: Human Translation
ms.sourcegitcommit: e7d3c82e235d691c4ab329be3b168dcccc19774f
ms.openlocfilehash: 5db1864f5fb04b30a7b8ce59932e826fcef792f0


---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>發生影響 Azure 雲端服務的 Azure 服務中斷事件時該怎麼辦
Microsoft 的同仁一向努力確保提供您需要的服務。 有時候因為不可抗力之影響，造成服務意外中斷。

Microsoft 為其服務提供服務等級協定 (SLA)，作為執行時間和連接承諾。 個別的 Azure 服務 SLA 位於 [Azure 服務等級協定](https://azure.microsoft.com/support/legal/sla/)。

Azure 已經有許多支援高可用性應用程式的內建平台功能。 如需這些服務的詳細資訊，請參閱 [Azure 應用程式的災害復原與高可用性](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)。

本文內含當整個地區因重大天災或大規模服務中斷而發生中斷的真實災害復原案例。 這些都是極其罕見的情況，但您還是必須對整個區域發生中斷的可能性有所準備。 如果整個區域的服務中斷，會暫時無法使用本機的備援資料複本。 如果已啟用異地複寫，會在不同的區域儲存額外三份 Azure 儲存體 Blob 和資料表。 如果發生全面性區域中斷或嚴重損壞，且主要區域無法復原時，Azure 會重新對應異地複寫區域的所有 DNS 項目。

> [!NOTE]
> 請注意，您完全無法控制這個程序，而且它只有在整個資料中心服務中斷時才會發生。 因此，您也必須依賴其他的應用程式特定備份策略，以達到最高層級的可用性。 如需詳細資訊，請參閱[建置在 Microsoft Azure 上之應用程式的災害復原和高可用性](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)。 如果您希望能夠影響您自己的容錯移轉，就可能想要考慮使用 [讀取存取異地備援儲存體 (RA-GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage)，在其他區域中建立資料的唯讀複本。
>
>

為協助您處理這些罕見事件，我們提供以下 Azure 虛擬機器 (VM) 指引，以因應 Azure VM 應用程式部署所在的整個區域發生服務中斷的情況。

## <a name="option-1-wait-for-recovery"></a>選項 1︰等待復原
在此情況下，您不需要採取任何動作。 但您要知道，Azure 小組正在努力還原服務的可用性。 您可以在 [Azure 服務健康狀態儀表板](https://azure.microsoft.com/status/)上看見目前的服務狀態。

> [!NOTE]
> 如果客戶尚未安裝 Azure Site Recovery，或在不同地區有次要部署，這會是最佳的選項。
>
>

如果客戶想要立即存取其已部署的雲端服務，目前提供下列選項。

> [!NOTE]
> 請注意，這兩個選項都可能會遺失部分資料。     
>
>

## <a name="option-2-re-deploy-your-cloud-service-configuration-to-a-new-region"></a>選項 2︰將您的雲端服務組態重新部署到新的區域
如果您具有原始程式碼，只需在新區域中將應用程式及相關聯的組態和資源重新部署至新的雲端服務。  

如需如何建立和部署雲端服務應用程式的詳細資訊，請參閱[如何建立和部署雲端服務](cloud-services-how-to-create-deploy-portal.md)。

根據您的應用程式資料來源，您可能需要檢查應用程式資料來源的復原程序。

* 如需 Azure 儲存體資料來源，請參閱 [Azure 儲存體複寫](../storage/storage-redundancy.md#read-access-geo-redundant-storage) ，以根據您針對應用程式所選擇的複寫模型來查看可用的選項。
* 如需 SQL 資料庫來源，請閱讀 [概觀：雲端商務持續性和 SQL Database 的資料庫災害復原](../sql-database/sql-database-business-continuity.md) ，以根據您針對應用程式所選擇的複寫模型來查看可用的選項。

## <a name="option-3-use-a-backup-deployment-through-azure-traffic-manager"></a>選項 3︰透過 Azure 流量管理員使用備份部署
此選項假設您在設計應用程式方案時已將區域災害復原納入考慮。 如果您已經在不同區域中執行次要的雲端服務應用程式部署，並透過流量管理員通道來連接，則您可以使用這個選項。 在此情況下，請檢查次要部署的健全狀況。 如果是狀況良好，您就可以透過 Azure 流量管理員將流量重新導向到該部署。 採用這個策略時，您就能利用 Azure 流量管理員中的流量路由方法和容錯移轉順序組態。 如需詳細資訊，請參閱[什麼是流量管理員？](../traffic-manager/traffic-manager-overview.md)。

![使用 Azure 流量管理員平衡區域間的 Azure 雲端服務](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

## <a name="next-steps"></a>後續步驟
若要深入了解如何實作災害復原和高可用性策略，請參閱 [Azure 應用程式的災害復原和高可用性](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)。

若要開發雲端平台功能的詳細技術知識，請參閱 [Azure 復原技術指導](../resiliency/resiliency-technical-guidance.md)。

如果指示不清楚，或如果您希望 Microsoft 代您執行作業，請連絡 [客戶支援](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。



<!--HONumber=Nov16_HO3-->


