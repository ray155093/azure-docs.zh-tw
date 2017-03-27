---
title: "什麼是 Azure SQL Database 服務？ | Microsoft Docs"
description: 'Get an introduction to SQL Database: technical details and capabilities of Microsoft''s relational database management system (RDBMS) in the cloud.'
keywords: "sql 簡介,sql 簡介,什麼是 sql database"
services: sql-database
documentationcenter: 
author: shontnew
manager: jhubbard
editor: cgronlun
ms.assetid: c561f600-a292-4e3b-b1d4-8ab89b81db48
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/17/2017
ms.author: shkurhek
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: c505844cc2b7c745a1106b3c446833fb206ca98a
ms.lasthandoff: 03/18/2017

---
# <a name="what-is-sql-database-introduction-to-sql-database"></a>什麼是 SQL Database？ SQL Database 簡介
SQL Database 是 Microsoft Cloud 中以領先市場的 Microsoft SQL Server 引擎為基礎的關聯式資料庫服務，能夠處理各種任務關鍵性工作負載。 SQL Database 提供多個服務等級的可預測效能、無停機時間的動態延展性、內建商務持續性以及資料保護功能，且全都幾乎免管理。 這些功能可讓您專注於快速開發應用程式及加快上市時間，而不是將寶貴的時間和資源耗費在管理虛擬機器和基礎結構上。 由於 SQL Database 是以 [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx) 引擎為基礎，所以可支援現有的 SQL Server 工具、程式庫和 API。 因此，您可以輕鬆地開發新的解決方案、移動現有的 SQL Server 解決方案，以及將現有的 SQL Server 解決方案延伸至 Microsoft Cloud，而不必學習新的技能。

本文介紹與效能、延展性和管理能力相關的 SQL Database 核心概念和功能，並提供連結讓您進一步了解詳細資料。 請參閱下列快速入門，以便開始使用產品：
 - [在 Azure 入口網站中建立 SQL Database](sql-database-get-started-portal.md)  
 - [使用 Azure CLI 建立 SQL Database](sql-database-get-started-cli.md)
 - [使用 PowerShell 建立 SQL Database](sql-database-get-started-powershell.md)

如需一組 Azure CLI 和 PowerShell 範例，請參閱︰
 - [Azure SQL Database 的 Azure CLI 範例](sql-database-cli-samples.md)
 - [Azure SQL Database 的 Azure PowerShell 範例](sql-database-powershell-samples.md)

## <a name="adjust-performance-and-scale-without-downtime"></a>無須停機即可調整效能和規模
SQL Database 提供三個服務層：基本、標準和進階。 每個服務層提供[不同層級的效能和功能](sql-database-service-tiers.md)，以支援輕量到重量級的資料庫工作負載。 您可以在小型資料庫中建置第一個應用程式，一個月只需少許花費。接著可隨時以手動或程式設計方式[變更其服務層](sql-database-service-tiers.md)，以符合您的方案需求。 當您這麼做的時候，您的應用程式或客戶皆無須停機。 動態延展性可讓您的資料庫以透明的方式回應快速變化的資源需求，並且讓您只需支付您所需的資源費用。

## <a name="elastic-pools-to-maximize-resource-utilization"></a>可將資源使用量最大化的彈性集區
對於許多企業和應用程式而言，只要能夠建立單一資料庫，並依需求調高或調低的效能即可，尤其是當使用模式相當容易預測時更是如此。 但如果您有無法預測的使用模式，則管理成本和商務模式就會變得相當困難。 [彈性集區](sql-database-elastic-pool.md)的設計可解決此問題。 概念很簡單。 您可將效能資源配置到集區，而非個別的資料庫，並支付該集區的集體效能資源，而非單一資料庫效能的費用。 使用彈性集區，您就不必隨著資源的需求波動，專注於調高或調低資料庫效能。 集區中的資料庫會視需要取用彈性集區的效能資源。 集區中的資料庫會取用集區的資源，但不會超過其限制，因此您的成本可在個別資料庫使用情形無法預測的狀況下維持可預測性。 此外，您還可以 [將資料庫移入/移出集區](sql-database-elastic-pool-manage-portal.md)，並將您的應用程式從數個資料庫擴充至數千個，而且全都在您可掌控的預算之內。 最後，您也可以控制集區中資料庫可用的資源上限和下限，以確保集區中不會有任一資料庫使用所有的集區資源，且集區中的每個資料庫都有保證的資源數量下限。 若要深入了解使用彈性集區的 SaaS 應用程式的設計模式，請參閱 [採用 Azure SQL Database 的多租用戶 SaaS 應用程式的設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)。

## <a name="blend-single-databases-with-pooled-databases"></a>混合使用單一資料庫與集區中的資料庫
不論您要使用單一資料庫或彈性集區，都將不再受到限制。 您可以混合使用單一資料庫與彈性集區，並快速且輕易地變更單一資料庫和彈性集區的服務層來適應您的情況。 此外，透過 Azure 功能強大而無遠弗屆的特性，您可以使用 SQL Database 混合和搭配其他 Azure 服務，滿足您獨特應用程式的設計需求、有效運用成本和資源，並且產生新的商機。

## <a name="monitoring-and-alerting"></a>監視和警示
但是，您要如何比較單一資料庫和彈性集區的相對效能？ 當您調高和調低效能時，要如何知道該在何處停止？ 您可使用[內建的效能監視](sql-database-performance.md)和[警示](sql-database-insights-alerts-portal.md)工具，加上以[適用於單一資料庫的資料庫交易單位 (DTU) 及適用於彈性集區的彈性 DTU (eDTU)](sql-database-what-is-a-dtu.md) 為根據的效能分級。 使用這些工具，您可以根據目前或專案的效能需求快速評估相應增加或減少的影響。 如需詳細資訊，請參閱 [SQL Database 選項和效能：了解每個服務層中可用的項目](sql-database-service-tiers.md) 。

## <a name="keep-your-app-and-business-running"></a>讓您的應用程式和業務持續運作
Azure 領先業界的 99.99% 可用性服務等級協定 [(SLA)](http://azure.microsoft.com/support/legal/sla/)(由 Microsoft 管理之資料中心的全球網路提供支援)，可協助讓您的應用程式 24 小時全年無休地運作。 使用每個 SQL Database，您可以利用內建的安全性、容錯功能，以及可能需要另外設計、購買、建置和管理的[資料保護](sql-database-automated-backups.md)功能。 使用 SQL Database 時，每個服務層都會提供一組完整的業務持續性功能和選項，讓您能夠用來啟動和執行，並持續維持該狀態。 您可以使用[時間點還原](sql-database-recovery-using-backups.md)將資料庫回復成先前的狀態，最久可至 35 天前。 您可以設定[長期的備份保留](sql-database-long-term-retention.md)，將備份儲存在安全的保存庫中長達 10 年。 此外，如果裝載資料庫的資料中心發生中斷情形，您可以從[最新備份的異地備援複本](sql-database-recovery-using-backups.md)還原資料庫。 如有需要，您也可以在一或多個區域中設定[異地備援可讀取的複本](sql-database-geo-replication-overview.md)，以便在資料中心發生中斷時快速容錯移轉。 您也可以使用這些複本，提升不同地理區域中的讀取效能，或進行[應用程式升級而不需要停機](sql-database-manage-application-rolling-upgrade.md)。 

![SQL Database 異地複寫](./media/sql-database-technical-overview/azure_sqldb_map.png)

如需可用於不同服務層之其他商務持續性功能的詳細資料，請參閱 [商務持續性](sql-database-business-continuity.md) 。

## <a name="secure-your-data"></a>保護您的資料
SQL Server 的資料安全性傳統是 SQL Database 支援多項功能，可限制存取權、保護資料，以及協助您監視活動。 如需可在 SQL Database 中使用之安全性選項的快速概要，請參閱 [保護您的 SQL Database](sql-database-security-overview.md) 。 如需更完整的安全性功能檢視，請參閱 [SQL Server Database Engine 和 SQL Database 的資訊安全中心](https://msdn.microsoft.com/library/bb510589) 。 如需 Azure 平台安全性的相關資訊，也請造訪 [Azure 信任中心](https://azure.microsoft.com/support/trust-center/security/) 。

## <a name="next-steps"></a>後續步驟
您現已閱讀 SQL Database 簡介並回答了「什麼是 SQL Database？」問題，您就可以：

* 如需單一資料庫及彈性資料庫的成本比較和計算機，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/sql-database/)。
* 深入了解 [彈性集區](sql-database-elastic-pool.md)。
* 從 [建立您的第一個資料庫](sql-database-get-started.md)開始。
* 以 C#、Java、Node.js、PHP、Python 或 Ruby 建置您的第一個應用程式： [SQL Database 和 SQL Server 的連接庫](sql-database-libraries.md)

