<properties
	pageTitle="發生影響 Azure 雲端服務的 Azure 服務中斷事件時該怎麼辦 | Microsoft Azure"
	description="了解發生影響 Azure 雲端服務的 Azure 服務中斷事件時該怎麼辦。"
	services="cloud-services"
	documentationCenter=""
	authors="kmouss"
	manager="drewm"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="cloud-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="kmouss;aglick"/>

#發生影響 Azure 雲端服務的 Azure 服務中斷事件時該怎麼辦

Microsoft 的同仁一向努力確保提供您需要的服務。有時候因為不可抗力之影響，造成服務意外中斷。

Microsoft 為其服務提供服務等級協定 (SLA)，作為執行時間和連接承諾。個別的 Azure 服務 SLA 位於 [Azure 服務等級協定](https://azure.microsoft.com/support/legal/sla/)下方。

Azure 有許多支援高可用性應用程式的內建平台功能。如需這些服務的詳細資訊，請參閱 [Azure 應用程式的災害復原和高可用性](https://aka.ms/drtechguide)。

本文件涵蓋當整個區域因重大天災或大規模服務中斷而發生中斷的真實災害復原。這些都是極其罕見的情況，但您還是必須對整個區域發生中斷的可能性有所準備。如果整個區域的服務中斷，會暫時無法使用本機的備援資料複本。如果已啟用異地複寫，會在不同的區域儲存三份 Azure 儲存體 Blob 和資料表。如果發生全面性區域中斷或嚴重損壞，且主要區域無法復原時，Azure 會重新對應異地複寫區域的所有 DNS 項目。
 
>[AZURE.NOTE]請注意，您完全無法控制這個程序，而且它只有在整個資料中心失敗時才會發生。因此，您也必須依賴其他的應用程式特定備份策略，以達到最高層級的可用性。如需詳細資訊，請參閱 [災害復原的資料策略](https://aka.ms/drtechguide#DSDR)一節。如果您希望能夠影響您自己的容錯移轉，就可能想要考慮使用[讀取存取異地備援儲存體 (RA-GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage)，在其他區域中建立資料的唯讀複本。

為協助您處理這些罕見事件，我們提供以下 Azure 虛擬機器指引，以因應 Azure 虛擬機器應用程式部署所在的整個區域發生服務中斷的情況。

##選項 1︰等待復原 
在此情況下，您不需要採取任何動作。但您要知道，Azure 小組正在努力還原服務的可用性。您可以在 [Azure 服務健康狀態儀表板](https://azure.microsoft.com/status/)上看見目前的服務狀態。

>[AZURE.NOTE]如果客戶尚未安裝 Azure Site Recovery (ASR)，或在不同地區有次要部署，這會是最佳的選項。

如果客戶想要立即存取其已部署的雲端服務，目前提供下列兩個選項。

>[AZURE.NOTE]請注意，這兩個選項都可能會遺失部分資料。

##選項 2︰將您的雲端服務組態重新部署到新的區域 

如果您具有原始程式碼，只需在新的區域中將應用程式及相關聯的組態和資源重新部署至新的雲端服務。

如需如何建立和部署雲端服務應用程式的詳細資訊，請參閱[如何建立和部署雲端服務](./cloud-services-how-to-create-deploy-portal.md)。

根據您的應用程式資料來源，您可能需要檢查應用程式資料來源的復原程序。
  * 如需 Azure 儲存體資料來源，請參閱 [Azure 儲存體複寫](../storage/storage-redundancy.md#read-access-geo-redundant-storage)，以根據您針對應用程式所選擇的複寫模型來查看可用的選項。
  * 如需 SQL 資料庫來源，請閱讀[概觀：雲端商務持續性和 SQL Database 的資料庫災害復原](../sql-database/sql-database-business-continuity.md)，以根據您針對應用程式所選擇的複寫模型來查看可用的選項。 

##選項 3︰透過流量管理員使用備份部署 
此選項假設您在設計應用程式方案時已將區域災害復原納入考慮。如果您已經在不同區域中執行次要的雲端服務應用程式部署，並透過流量管理員通道來連接，則可使用這個選項。在此情況下，請檢查次要部署的健康狀態，如果全都狀況良好，您就可以透過 Azure 流量管理員，在 ATM 中利用流量路由方法和容錯移轉順序組態，將流量重新導向至其中，請參閱[如如何進行流量管理員設定](../traffic-manager/traffic-manager-overview.md#how-to-configure-traffic-manager-settings)。

![使用 Azure 流量管理員平衡區域間的 Azure 雲端服務](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

##參考 

[Azure 應用程式災害復原與高可用性](https://aka.ms/drtechguide)

[Azure 業務持續性技術指引](https://aka.ms/bctechguide)
 
如果指示不清楚，或如果您希望 Microsoft 代您執行作業，請連絡[客戶支援](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

<!---HONumber=AcomDC_0525_2016-->