---
title: "監視多租用戶 SaaS 應用程式中許多 Azure SQL Database 的效能 | Microsoft Docs"
description: "監視及管理 Azure SQL Database 範例 Wingtip SaaS 應用程式的效能"
keywords: SQL Database Azure
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 54f29cc816d356e22b425f3824ef89800c017e61
ms.contentlocale: zh-tw
ms.lasthandoff: 05/25/2017


---
# <a name="monitor-performance-of-the-wingtip-saas-application"></a>監視 Wingtip SaaS 應用程式的效能

在本教學課程中，將示範 SQL Database 和彈性集區的內建監視與警示功能，以及探索 SaaS 應用程式中所使用的數個關鍵效能管理案例。

Wingtip SaaS 應用程式使用單一租用戶資料模型，其中每個場地 (租用戶) 都有各自的資料庫。 與許多 SaaS 應用程式類似，預期的租用戶工作負載模式無法預測且偶爾發生。 換句話說，票證銷售可能會在任何時間發生。 若要利用這種一般資料庫使用模式，租用戶資料庫要部署到彈性資料庫集區中。 彈性集區可透過跨多個資料庫共用資源，來最佳化解決方案的成本。 使用這種類型的模式，請務必監視資料庫和集區資源使用量，以確保合理地跨集區平衡負載。 您也必須確定個別的資料庫擁有足夠的資源，且集區未達到其 [eDTU](sql-database-what-is-a-dtu.md) 限制。 本教學課程將探討監視及管理資料庫與集區的方式，以及如何採取矯正措施以回應工作負載的變化。

在本教學課程中，您將了解如何：

> [!div class="checklist"]

> * 透過執行提供的負載產生器，來模擬租用戶資料庫上的使用量
> * 在租用戶資料庫回應負載增加時監視它們
> * 相應增加彈性集區，以回應增加的資料庫負載
> * 佈建第二個彈性集區以針對資料庫活動進行負載平衡


若要完成本教學課程，請確定已完成下列必要條件：

* 已部署 Wingtip SaaS 應用程式。 若要在五分鐘內完成部署，請參閱[部署及探索 Wingtip SaaS 應用程式](sql-database-saas-tutorial.md)
* 已安裝 Azure PowerShell。 如需詳細資料，請參閱[開始使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>SaaS 效能管理模式簡介

管理資料庫效能包含了編譯及分析效能資料，然後透過調整參數回應此資料，以維持您應用程式的可接受回應時間。 裝載多個租用戶時，彈性資料庫集區是一種符合成本效益的方式，可針對包含無法預測之工作負載的資料庫群組提供及管理資源。 使用特定工作負載模式，最少為兩個 S3 資料庫可以受惠於在集區中管理。 集區不僅分攤資源的成本，它也可以讓您不需要一直監視及追蹤個別資料庫。

![媒體](./media/sql-database-saas-tutorial-performance-monitoring/app-diagram.png)

您仍然需要監視集區和集區中的資料庫，以確保它們的效能在可接受範圍內。 調整集區設定以符合彙總工作負載的需求，確保集區 eDTU 適用於整體工作負載。 針對您的特定應用程式需求，將每個資料庫最小和最大 eDTU 值調整為適當的值。

### <a name="performance-management-strategies"></a>效能管理策略

* 若要避免手動監視效能，最有效的方式是**設定在資料庫或集區偏離正常範圍時引發警示**。
* 若要回應集區彙總效能層級中的短期波動，**集區 eDTU 層級可以相應增加或減少**。 如果這樣的波動是以定期或可預測的基礎發生，則**可排定自動調整集區**。 例如，當您知道夜間或者週末期間工作負載較輕時，就可以相應減少。
* 若要回應長期波動或多個資料庫中的變更，**個別資料庫可以移至其他集區**。
* 若要回應「個別」資料庫負載中的短期增加，**可由集區中取出個別資料庫並針對某段期間指派個別效能等級**。 一旦負載降低後，就可以讓資料庫返回到集區。 事先知道時，則能預先移動資料庫以確保資料庫一律擁有它所需的資源，和避免影響集區中的其他資料庫。 如果可預測此需求，例如場地因熱門活動而發生票券銷售熱潮，則此管理行為可以與應用程式整合。

[Azure 入口網站](https://portal.azure.com)提供大部分資源的內建監視與警示功能。 針對 SQL Database，資料庫與集區提供監視與警示功能。 這個內建的監視與警示功能是資源特定，因此針對少數資源使用很方便，但是搭配許多資源使用時則不是很方便。

針對處理大量資源的情況，可以使用 Log Analytics (亦稱為 OMS)。 這是個別的 Azure 服務，可針對發出的診斷記錄和記錄檔分析工作區中收集的遙測提供分析，這可以收集來自許多服務的遙測並用來查詢及設定警示。

## <a name="get-the-wingtip-application-scripts"></a>取得 Wingtip 應用程式指令碼

在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) Github 存放庫可取得 Wingtip Tickets 指令碼和應用程式原始程式碼。 [用於下載 Wingtip SaaS 指令碼的步驟](sql-database-wtp-overview.md#download-the-wingtip-saas-scripts)。

## <a name="provision-additional-tenants"></a>佈建其他租用戶

因為集區在只有兩個 S3 資料庫的時候符合成本效益，所以集區中有越多資料庫則平均效果越符合成本效益。 為了深入了解效能監視與管理大規模運作的方式，本教學課程要求您至少部署 20 個資料庫。

如果您已在先前的教學課程中佈建批次的租用戶，您可以跳至[模擬所有租用戶資料庫上的使用量](#simulate-usage-on-all-tenant-databases)一節。

1. 在 [PowerShell ISE] 中開啟 …\\Learning Modules\\Provision and Catalog\\*Demo-PerformanceMonitoringAndManagement.ps1*。 保持此指令碼開啟，因為您將在本教學課程期間執行數個案例。
1. 設定 **$DemoScenario** = **1**，**佈建批次的租用戶**
1. 按 **F5** 以執行指令碼。

指令碼將在五分鐘內部署 17 個租用戶。

*New-TenantBatch* 指令碼會使用建立租用戶批次之 [Resource Manager](../azure-resource-manager/index.md) 範本的巢狀或連結集合，根據預設會複製類別目錄伺服器上的 **baseTenantDb** 資料庫，以建立新的租用戶資料庫，然後在類別目錄中註冊這些資料庫，最後則使用租用戶名稱和場地類型將它們初始化。 這與應用程式佈建新租用戶的方式一致。 針對 *baseTenantDB* 所做的任何變更會套用至之後所佈建的任何新租用戶。 請參閱[結構描述管理教學課程](sql-database-saas-tutorial-schema-management.md)，以了解如何針對「現有」租用戶資料庫 (包括「標準」資料庫) 進行結構描述變更。

## <a name="simulate-different-usage-patterns-by-generating-different-load-types"></a>透過產生不同的負載類型來模擬不同的使用模式

*Demo-PerformanceMonitoringAndManagement.ps1* 指令碼會使用下列其中一種可用的「負載類型」啟動負載產生器：

| 示範 | 案例 |
|:--|:--|
| 2 | 產生一般強度負載 (大約 40 個 DTU) |
| 3 | 每個資料庫產生時間更長、更頻繁高載的負載|
| 4 | 每個資料庫產生具有更高 DTU 高載的負載 (大約 80 個 DTU)|
| 5 | 在單一租用戶上產生一般負載加上高負載 (大約 95 個 DTU)|
| 6 | 產生跨多個集區的不對稱負載|

## <a name="simulate-usage-on-all-tenant-databases"></a>模擬所有租用戶資料庫的使用情形

負載產生器會將僅限「綜合」 CPU 的負載套用到每一個租用戶資料庫。 產生器會針對每個租用戶資料庫啟動作業，這會定期呼叫產生負載的預存程序。 負載層級 (單位為 eDTU)、持續時間和間隔會跨所有的資料庫而變動，以模擬無法預測的租用戶活動。

1. 設定 **$DemoScenario** = **2**，*產生一般強度負載*。
1. 按下 **F5** 將負載套用到您的所有租用戶資料庫。

因為負載偶發性的本質，請讓產生器執行 10-20 分鐘，以便活動能達到穩定狀態，並順利進入良好的模式。

> [!IMPORTANT]
> 負載產生器正於您的本機 PowerShell 工作階段中以一系列作業的方式執行。 保持 [Demo-PerformanceMonitoringAndManagement.ps1] 索引標籤開啟！ 如果您關閉該索引標籤或暫停您的電腦，負載產生器將會停止。

## <a name="monitor-resource-usage-using-the-portal"></a>使用入口網站監視資源使用量

若要監視從已套用的負載所產生的資源使用量，請開啟入口網站並移至包含租用戶資料庫的集區。

1. 開啟 [Azure 入口網站](https://portal.azure.com)並瀏覽至 tenants1-&lt;使用者&gt; 伺服器。
1. 您應該會看到租用戶資料庫清單，包括新批次的資料庫。
1. 向下捲動並找出彈性集區，然後按一下 [Pool1]。 此集區包含到目前為止所建立的所有租用戶資料庫。
1. 展開隨即開啟的集區刀鋒視窗，並觀察集區使用量圖表和熱門資料庫使用量圖表。

集區使用量基本上是集區中所有資料庫的資料庫使用量彙總。 資料庫使用量圖表會顯示最熱門的 5 個資料庫︰

![](./media/sql-database-saas-tutorial-performance-monitoring/pool1.png)

因為集區中前 5 名資料庫以外還有其他資料庫，集區使用量會顯示未反映在前五個資料庫圖表中的活動。 若要查看其他詳細資料，請按一下 [資料庫資源使用量]：

![](./media/sql-database-saas-tutorial-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>設定集區上的效能警示

在集區上設定 \>75% 使用率維持 5 分鐘時觸發的警示，如下所示：

1. 在 [Azure 入口網站](https://portal.azure.com)中開啟 *Pool1* (在 *tenants1-\<使用者\>* 伺服器上)。
1. 按一下 [警示規則]，然後按一下 [+ 加入警示]：

   ![加入警示](media/sql-database-saas-tutorial-performance-monitoring/add-alert.png)

1. 提供名稱，例如「高 DTU」， 
1. 設定下列值：
   * **度量 = eDTU 百分比**
   * **條件 = 大於**。
   * **閾值 = 75**。
   * **期間 = 過去 30 分鐘內**。

   ![設定警示](media/sql-database-saas-tutorial-performance-monitoring/alert-rule.png)

您可以讓通知傳送至您的 Azure 帳戶電子郵件，以及選擇性的其他額外電子郵件 (建議不要設定，除非您擁有正在使用的訂用帳戶)。

> [!NOTE]
> 因為警示只會在過去 30 分鐘內閾值已超過時引發，所以負載產生器必須執行超過 30 分鐘才能測試警示。


## <a name="scale-up-a-busy-pool"></a>相應增加忙碌的集區

如果集區上的彙總負載層級增加到超出集區最大值且達到 100% eDTU 使用率，則個別資料庫效能會受到影響，可能拖慢集區中所有資料庫的查詢回應時間。

就短期來說，請考慮相應增加集區以提供其他資源，或從集區移除資料庫 (將資料庫移動至其他集區，或從集區移出至獨立的服務層)。

就長期來說，請考慮最佳化查詢或使用索引來改善資料庫效能。 根據應用程式對效能問題的敏感度，最佳做法是在集區達到 100% eDTU 使用量之前相應增加它。 使用警示來事先警告您。

您可以藉由增加產生器所產生的負載，模擬忙碌的集區。 讓資料庫高載時間更頻繁且更長，增加集區的彙總負載而不變更個別資料庫的需求。 在入口網站或從 PowerShell 可以輕鬆地相應增加集區。 此練習使用入口網站。

1. 設定 *$DemoScenario* = **3**，_每個資料庫產生時間更長、更頻繁高載的負載_，以增加集區的彙總負載強度，而不變更每個資料庫所需的尖峰負載。
1. 按下 **F5** 將負載套用到您的所有租用戶資料庫。


1. **開啟** **tenants1/Pool1 的集區刀鋒視窗**。


1. 監視上方圖表上增加的集區 DTU 使用量。 需要花數分鐘讓新的高負載開始作用，但您應該很快會看到集區開始達到 100% 使用率，而隨著負載穩定進入新的模式，它會快速地使集區多載。


1. 若要相應增加集區，按一下 [設定集區]****
1. 調整 [集區 eDTU] 滑桿至 100 (建議您不要設為更高的值，以限制成本)。 請注意，集區中所有資料庫的可用彙總儲存體 (由 [集區 GB] 指示) 如何連結到 eDTU 設定，而且也會增加。 變更集區 eDTU 不會變更每個資料庫設定 (仍然是每個資料庫最高 50 eDTU)。 您可以在 [設定集區] 刀鋒視窗右側看到每個資料庫的設定。
1. 按一下 [儲存] 以提交要求。 針對「標準」集區，變更通常需要花 3-5 分鐘的時間。

返回至 **Pool1** > **概觀** 以檢視監視圖表。 監視為集區提供更多資源的影響 (雖然有一些資料庫和隨機負載，也不容易明確地看出)。 當您查看圖表時，請記住上方圖表上的 100% 現在代表 100 個 eDTU，而下方圖表上的 100% 仍是 50 個 eDTU，因為每個資料庫最大值仍是 50 個 eDTU。

整個過程中，資料庫會維持連線且完全可供使用。 最後每個資料庫都準備好以新的集區 eDTU 啟用時，任何使用中的連線都會中斷。 應用程式程式碼應該一律撰寫為重試中斷的連線，且因此重新連線至相應增加集區中的資料庫。

## <a name="create-a-second-pool-and-load-balance-databases-to-handle-increased-aggregate-load"></a>建立第二個集區和負載平衡資料庫，以處理增加的彙總負載

做為相應增加集區的替代方案，您可以建立第二個集區並將資料庫移到其中，以平衡兩個集區之間的負載。 若要這樣做，必須在與第一個集區相同的伺服器上建立新的集區。

1. 開啟 **customers1-&lt;使用者&gt; 伺服器的伺服器刀鋒視窗**。 如果您在資料庫或集區刀鋒視窗上，則可以卸除程式集控制項，並選取伺服器名稱做為捷徑。
1. 按一下 [+ 新增集區] 以在目前的伺服器上建立集區
1. 在新的彈性資料庫集區範本上︰

    1. 設定**名稱 = Pool2**。
    1. 保留定價層為**標準集區**。
    1. 按一下 [設定集區]，
    1. 在開啟的 [設定集區] 刀鋒視窗上，設定 [集區 eDTU = 50 個 DTU]。
    1. 按一下 [加入資料庫] 命令，以查看此伺服器上不在目前集區中之資料庫的清單。
    1. 在清單中，「勾選」一半的資料庫 (20 個中的 10 個) 以將這些資料庫移動至新的集區，然後按一下 [選取]。
    1. 再次按一下 [選取]，以接受設定變更。 請注意，會使用選取的選項針對一個月的使用量執行成本估計。
    1. 選取 [確定] 以建立使用新設定的新集區，並移動資料庫。

建立集區並將資料庫移到其中需要數分鐘的時間。 每個移動的資料庫會維持連線並完全可供存取直到最後一刻，屆時會關閉任何開啟的連線。 當用戶端重試連線時，會連線至新集區中的資料庫。

集區建立之後，它會顯示在 customers1 伺服器刀鋒視窗上。 按一下集區名稱以開啟集區刀鋒視窗，並監視其效能。

您應該會看到 Pool1 上的資源使用量已下降且 Pool2 維持同樣的負載。

## <a name="manage-an-increased-load-on-a-single-database"></a>管理單一資料庫上增加的負載

如果集區中的單一資料庫發生持續的高負載，根據集區設定，它可能傾向於支配該集區中的資源，並影響其他資料庫。 如果活動可能會持續一陣子，資料庫可以暫時移出集區。 這可同時讓該資料庫擁有比集區中其他資料庫更多的資源，並將它與其他資料庫隔離。 本練習會模擬在銷售熱門音樂會的票券時，Contoso 演藝廳發生高負載的效果。

1. 在 …\\**Demo-PerformanceManagementAndMonitoring**.ps1 指令碼中
1. 設定 **$DemoScenario = 5，在單一租用戶上產生一般負載加上高負載 (大約 95 個 DTU)**。
1. 設定 **$SingleTenantDatabaseName = contosoconcerthall**
1. 使用 **F5** 執行指令碼。
1. **開啟** **Customers1/Pool1 的集區刀鋒視窗**。
1. 查看該刀鋒視窗頂端的 [彈性集區監視] 顯示，並尋找增加的集區 DTU 使用量。 在一或兩分鐘後，應該會開始產生較高的負載，且您應該會快速看到集區達到 100% 使用率。
1. 同時也監視 [彈性資料庫監視] 顯示，它會顯示過去一小時內最熱門的資料庫。 contosoconcerthall 資料庫應該很快會顯示為 5 個最熱門資料庫的其中之一。
1. **按一下彈性資料庫監視****圖表**會開啟 [資料庫資源使用量] 刀鋒視窗，您可以在該刀鋒視窗上選擇性地監視任何資料庫。 這可讓您找出 contosoconcerthall 資料庫的顯示。
1. 從資料庫清單中，**按一下 contosoconcerthall** 會開啟其資料庫刀鋒視窗。
1. 按一下操作功能表中的 [定價層 (調整 DTU)] 以開啟 [設定效能] 刀鋒視窗，其中您可以設定資料庫的個別效能等級。
1. 按一下 [標準] 索引標籤，以開啟標準層中的調整選項。
1. 將 [DTU] 滑桿向右滑動以選取 100 個 DTU。 請注意，這會對應至服務目標 **S3** (會以 DTU 和儲存體大小計量之間的括弧顯示)。
1. 按一下 [套用] 將資料庫移出集區，並讓它成為標準 S3 資料庫。
1. 部署完成之後，在彈性集區和資料庫刀鋒視窗上監視 contosoconcert hall 資料庫以及移除它之集區的效果。

一旦 contosoconcerthall 資料庫上高於一般的負載下降，您應該立即讓它返回到集區，以降低其成本。 如果不確定何時會發生，您應該在資料庫上設定一個當其 DTU 使用量下降到低於集區上每個資料庫最大值時引發的警示。 練習 5 中說明如何將資料庫移入集區。

## <a name="other-performance-management-patterns"></a>其他效能管理模式

**預先調整** 在您探索如何調整隔離資料庫的練習 6 中，您知道要尋找的資料庫。 如果 Contoso 演藝廳管理已通知 Wingtips 即將發生的票證銷售，則資料庫可能已預先移出集區。 否則，它可能會需要集區或資料庫上的警示來了解發生的事件。 您不會想要從集區中其他抱怨效能降低的租用戶得知這件事。 而且如果租用戶可以預測需要其他資源多久的時間，您可以設定 Azure 自動化 Runbook 將資料庫移出集區，然後依定義的排程將它重新移回到集區中。

**租用戶自助調整** 因為調整是可以透過管理 API 輕鬆呼叫的工作，您可以輕易地在您的租用戶面向應用程式中建立調整租用戶資料庫的能力，並以 SaaS 服務的功能形式提供。 例如，讓租用戶自行管理相應增加和減少，或許會直接影響其計費！

### <a name="scaling-a-pool-up-and-down-on-a-schedule-to-match-usage-patterns"></a>依排程相應增加和減少集區，以符合使用模式

當彙總租用戶使用量遵循可預測的使用模式時，您可以使用「Azure 自動化」依排程相應增加和減少集區。 例如，當您知道資源需求會下降時，在工作日下午 6 點後相應減少集區，而在上午 6 點之前重新相應增加。



## <a name="next-steps"></a>後續步驟

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 透過執行提供的負載產生器，來模擬租用戶資料庫上的使用量
> * 在租用戶資料庫回應負載增加時監視它們
> * 相應增加彈性集區，以回應增加的資料庫負載
> * 佈建第二個彈性集區以針對資料庫活動進行負載平衡

[還原單一租用戶教學課程](sql-database-saas-tutorial-restore-single-tenant.md)


## <a name="additional-resources"></a>其他資源

* 其他[以 Wingtip SaaS 應用程式部署為基礎的教學課程](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [SQL 彈性集區](sql-database-elastic-pool.md)
* [Azure 自動化](../automation/automation-intro.md)
* [Log Analytics](sql-database-saas-tutorial-log-analytics.md) - 設定及使用 Log Analytics 教學課程
