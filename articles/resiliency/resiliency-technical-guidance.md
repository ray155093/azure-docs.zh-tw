<properties
   pageTitle="復原技術指導索引 | Microsoft Azure"
   description="有關了解和設計復原、高可用性、容錯的應用程式，及規劃災害復原和商務持續性的技術文章索引"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/13/2016"
   ms.author="patw;jroth;aglick"/>

#Azure 復原技術指導

##簡介

達到高可用性和災害復原需求需要兩種類型的知識：

- 對於雲端平台功能有詳細的技術上的了解
- 了解如何適當地建構分散式服務

這一系列的文章涵蓋前者：有關於恢復 (有時稱為商務持續性) Azure 平台的功能與限制。如果您對後者有興趣，請參閱著重於 [Azure 應用程式災害復原與高可用性](https://aka.ms/drtechguide)的系列文章。雖然本文章系列會牽涉到架構和設計模式，但這不是本系列的探討重心。如需設計指導，您可以查閱[其他資源](#additional-resources)小節中的資料。

該資訊歸納為下列文章：

- [從本機失敗復原](resiliency-technical-guidance-recovery-local-failures.md)。實體硬體 (例如磁碟機、伺服器和網路裝置) 可能會失效。負載尖峰時，可能會耗盡資源。本文說明在這些情況下，為了維持高可用性 Azure 提供的功能。

- [從 Azure 區域全面性服務中斷復原](resiliency-technical-guidance-recovery-loss-azure-region.md)。廣泛性的失敗較為罕見，但理論上可能發生。整個區域可能由於網路失敗而變得隔離，或因天然災害而導致實體損壞。本文說明如何使用 Azure 來建立跨地理區域的應用程式。

- [從內部部署復原到 Azure](resiliency-technical-guidance-recovery-on-premises-azure.md)。雲端已大幅改變災害復原的經濟體系，使得組織能夠利用 Azure 來建立第二個網站以進行復原。只要使用一部分的建置和維護次要資料中心的成本即可做到。本文將說明針對將內部部署資料中心擴充至雲端，Azure 所提供的功能。

- [從資料損毀或意外刪除復原](resiliency-technical-guidance-recovery-data-corruption.md)。應用程式可能有錯誤導致資料損毀。操作員可能不慎刪除重要資料。本文將說明 Azure 對於資料備份及還原為之前時間點所提供的內容。

##其他資源

- [建置在 Microsoft Azure 上之應用程式的災害復原和高可用性](resiliency-disaster-recovery-high-availability-azure-applications.md)。本文提供可用性和災害復原的詳細概觀。它涵蓋手動複寫參考資料和交易資料的挑戰。最後幾個小節摘要說明可跨越 Azure 區域以獲得最高層級可用性之不同類型的災害復原拓撲。

- [高可用性檢查清單](resiliency-high-availability-checklist.md)。本文列出可協助您提升應用程式復原能力和可用性的功能、服務及設計。

- [Microsoft Azure 服務恢復指引](resiliency-service-guidance-index.md)。本文是一份 Azure 服務索引，並且會提供災害復原指導及設計指導的連結。

- [概觀：雲端商務持續性和 SQL Database 的資料庫災害復原](../sql-database/sql-database-business-continuity.md)。本文提供 Azure SQL Database 可用性技術。其主要著重在備份與還原策略。如果您在雲端服務中使用 Azure SQL Database，應該檢閱本文和其相關的資源。

- [Azure 虛擬機器中的 SQL Server 高可用性和災害復原](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)。本文探討當您使用基礎結構即服務 (IaaS) 來裝載資料庫服務時，您可以探索的可用性選項。它討論 AlwaysOn 可用性群組、資料庫鏡像、記錄傳送，以及備份/還原。有幾個教學課程會說明如何使用這些技術。

- [Azure 雲端服務上大規模服務設計的最佳作法](https://azure.microsoft.com//blog/best-practices-for-designing-large-scale-services-on-windows-azure/)。本文將焦點放在開發高度可調整的雲端架構。您用來改善可用性的許多技術也有助於改善可調整性。此外，如果您的應用程式在負載增加時無法調整，可調整性就變成可用性問題。

- [Azure 虛擬機器中的 SQL Server 備份和還原](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md)。本文提供有關如何備份和還原「Azure 虛擬機器」上執行之 Microsoft SQL Server 的技術指引。

- [Failsafe︰恢復雲端架構指引](https://channel9.msdn.com/Series/FailSafe)。本文提供如何建置彈性雲端架構的指引、如何在 Microsoft 技術上實作這些架構的指引，以及針對特定案例實作這些架構的訣竅。

- [技術案例研究︰使用雲端技術來改善災害復原](https://www.microsoft.com/itshowcase/Article/Content/737/Using-cloud-technologies-to-improve-disaster-recovery)。此案例研究說明 Microsoft IT 如何使用 Azure 來改善災害復原。

##後續步驟

這篇文章是一系列文章的一部分，著重在 Azure 復原的技術指導。如果您有興趣閱讀本系列的其他文章，您可以從[從本機失敗中復原](resiliency-technical-guidance-recovery-local-failures.md)開始。

<!---HONumber=AcomDC_0615_2016-->