---
title: "部署及探索使用 Azure SQL Database 的 SaaS 應用程式 | Microsoft Docs"
description: "部署及瀏覽 Azure SQL Database 範例 Wingtip Tickets (WTP) 應用程式"
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
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 04859fb8a9f4a8bf04d92e7139d9202885c52503
ms.contentlocale: zh-tw
ms.lasthandoff: 05/12/2017


---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-azure-sql-database"></a>部署及探索使用 Azure SQL Database 的多租用戶 SaaS 應用程式

在此本教學課程中，您會部署及探索 Wingtip Tickets Platform (WTP) SaaS 應用程式。 應用程式使用租用戶各有資料庫的 SaaS 應用程式模式來維護多租用戶。 應用程式是設計成用來展示支援 SaaS 案例，以及 SaaS 設計及管理模式的 Azure SQL Database 功能。

按一下下面的 [部署至 Azure] 按鈕五分鐘之後，您會擁有一個在雲端執行的多租用戶 SaaS 應用程式 (使用 SQL Database)。 應用程式部署為包含三個範例租用戶，每個都有自己的資料庫，且全部都部署到 SQL 彈性集區中。 應用程式會部署到您的 Azure 訂用帳戶，讓您有完整的存取權可以檢查及使用個別應用程式元件。

在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) Github 存放庫可取得指令碼和應用程式原始程式碼。

您會在本教學課程中學到：

> [!div class="checklist"]

> * 如何部署 WTP 應用程式
> * 關於組成應用程式的伺服器、集區和資料庫
> * 租用戶會使用「類別目錄」來對應到其資料
> * 如何佈建新租用戶
> * 如何檢視集區使用率以監視租用戶活動
> * 如何刪除範例資源以停止相關計費

若要探索各種 SaaS 設計和管理模式，有以此初始部署為基礎而建置的[一系列相關教學課程](sql-database-wtp-overview.md)可供參考。 在進行教學課程的時候，您可以鑽研所提供的指令碼，並檢查不同 SaaS 模式的實作方式。 逐步執行每個教學課程中的指令碼，進而深入了解如何實作可簡化 SaaS 應用程式開發的多種 SQL Database 功能。

若要完成本教學課程，請確定已完成下列必要條件：

* 已安裝 Azure PowerShell。 如需詳細資料，請參閱[開始使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="deploy-the-wingtip-tickets-wtp-saas-application"></a>部署 Wingtip tickets (WTP) SaaS 應用程式

在五分鐘內部署 Wingtip Tickets Platform：

1. 按一下以部署：

   <a href="http://aka.ms/deploywtpapp" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. 輸入部署所需的參數值：

    > [!IMPORTANT]
    > 為了示範的目的，已刻意將某些驗證和伺服器防火牆設為不安全。 **建立新的資源群組**，而不要使用現有的資源群組、伺服器或集區，而且不要將此應用程式或它所建立的任何資源用於生產環境。 不使用應用程式之後請刪除此資源群組，以停止相關計費。

    * **資源群組** - 選取 [新建] 並提供 [名稱] 和 [位置]。
    * **使用者** - 某些資源需要跨所有 Azure 訂用帳戶都唯一的名稱。 若要確保唯一性，請提供值以區別您建立的資源，及其他部署 Wingtip 應用程式的使用者所建立的資源。 建議您使用簡短的 [使用者] 名稱，例如您的姓名縮寫加上數字 (例如 *bg1*)，然後將該名稱用於資源群組名稱 (例如 *wingtip-bg1*)。 [使用者] 參數只能包含字母、數字和連字號。 第一個和最後一個字元必須是字母或數字 (建議全部都小寫)。

     ![範本](./media/sql-database-saas-tutorial/template.png)

1. **部署應用程式**。

    * 如果同意條款及條件，請按一下。
    * 選取 [釘選到儀表板]。
    * 按一下 [購買]。

1. 按一下 [通知] (搜尋方塊右邊的鈴鐺圖示) 來監視部署狀態。 部署 WTP 應用程式大約需要四分鐘。

   ![部署成功](media/sql-database-saas-tutorial/succeeded.png)

## <a name="explore-the-application"></a>探索應用程式

應用程式會展示各種舉辦活動的場地 (例如，音樂廳、爵士俱樂部、運動俱樂部)。 成為 Wingtip Tickets Platform (WTP) 客戶 (或租用戶) 的場地註冊者，輕鬆就能列出場地並售票。 每個場地都有客製化的 Web 應用程式，可以管理並列出活動及售票 (獨立且與其他租用戶隔離)。 實際上，每個租用戶都會有一個部署到 SQL 彈性集區的 SQL 資料庫。

中央 [Events Hub (活動中心)] 提供部署專屬的租用戶 URL 清單。 所有租用戶 URL 都包含您的特定 [使用者] 值，且格式為 http://events.wtp.&lt;使用者&gt;.trafficmanager.net/*fabrikamjazzclub*。 

1. 開啟 [Events Hub (活動中心)]：http://events.wtp.&lt;使用者&gt;.trafficmanager.net (以您的 [使用者] 名稱取代)：

    ![events hub (活動中心)](media/sql-database-saas-tutorial/events-hub.png)

1. 按一下 [Events Hub (活動中心)] 中的 [Fabrikam Jazz Club (Fabrikam 爵士俱樂部)]。

   ![活動](./media/sql-database-saas-tutorial/fabrikam.png)

1. 按一下 [Tickets (票券)] 並探索可購買的活動票券。

WTP 應用程式使用 [*Azure 流量管理員*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)來控制連入流量的散發。 活動頁面 (租用戶專屬) 要求您必須在 URL中包含租用戶名稱。 活動應用程式會剖析 URL 中的租用戶名稱，並以它來建立使用[*分區對應管理*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-scale-shard-map-management)的類別目錄。 類別目錄會將鍵值對應到租用戶的資料庫位置。 [Events Hub (活動中心)] 會使用類別目錄中的延伸中繼資料來擷取與每個資料庫相關聯的租用戶名稱。

在生產環境中，您通常會建立 CNAME DNS 記錄以[*將公司網際網路網域指向*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain)流量管理員設定檔。

## <a name="get-the-wingtip-application-scripts"></a>取得 Wingtip 應用程式指令碼

在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) Github 存放庫可取得 Wingtip Tickets 指令碼和應用程式原始程式碼。 指令碼檔案位於 [[Learning Modules] 資料夾](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules)中。 請將 [Learning Modules] 資料夾下載到您的本機電腦，並維持其資料夾結構。

## <a name="provision-a-new-tenant"></a>佈建新租用戶

初始部署會建立三個範例租用戶，但為了更好的教學課程體驗，我們需要建立更多租用戶。 在此步驟中，您會快速建立新的租用戶。 您稍後應在[佈建和類別目錄教學課程](sql-database-saas-tutorial-provision-and-catalog.md)中，深入鑽研佈建新租用戶的詳細資料，您會在其中了解到在應用程式中實作註冊元件及在客戶註冊時自動佈建租用戶是多麼簡單。

### <a name="initialize-the-user-config-file-for-your-deployment"></a>初始化部署的使用者設定檔

因為這是在初始 WTP 應用程式部署之後執行的第一個指令碼，所以您必須更新使用者設定檔。 使用您的部署之特定值來更新變數。

   1. 在 [PowerShell ISE] 中開啟 ...\\Learning Modules\\*UserConfig.psm1*
   1. 將 _$userConfig.ResourceGroupName_ 修改成您部署應用程式時設定的「資源群組」。
   1. 將 _$userConfig.Name_ 修改成您部署應用程式時設定的「使用者」。

### <a name="provision-the-new-tenant"></a>佈建新租用戶

1. 在 [PowerShell ISE] 中，開啟 ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1*。
1. 按 **F5** 來執行指令碼 (目前先保持指令碼的預設值)。

新租用戶會註冊到類別目錄中。 系統會建立它們的資料庫，並新增到 SQL 彈性集區中。 成功佈建之後，瀏覽器中就會顯示新租用戶的售票網站：

![新租用戶](./media/sql-database-saas-tutorial/red-maple-racing.png)

重新整理 [Events Hub (活動中心)] 並確定新租用戶在清單中。

## <a name="start-generating-load-on-the-tenant-databases"></a>開始在租用戶資料庫上產生負載

既然我們已經有數個租用戶資料庫，就開始讓它們運作吧！ 提供的 PowerShell 指令碼會模擬對所有租用戶資料庫執行的工作負載。 負載預設會執行 60 分鐘。 Wingtip Tickets 是 SaaS 應用程式，而實際 SaaS 應用程式上的負載通常是偶爾發生且無法預測的。 為了模擬此情況，負載產生器會產生跨所有租用戶散發的隨機負載。 需要數分鐘才會出現模式，所以在嘗試監視以下各節中的負載時，請讓負載產生器執行大約 5-10 分鐘。

1. 在 [PowerShell ISE] 中，開啟 ...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.psm1*
1. 按 **F5** 來執行指令碼並啟動負載產生器 (目前先保留預設參數值)。

> [!IMPORTANT]
> 負載產生器會在您的本機 PowerShell 工作階段執行一系列的工作，所以請保持 PowerShell 工作階段開啟。 如果將 PowerShell (或負載產生器在其中啟動的索引標籤) 關閉，或將電腦暫停，工作就會停止。


## <a name="explore-the-servers-pools-and-tenant-databases"></a>探索伺服器、集區和租用戶資料庫

既然您已經探索應用程式、佈建新租用戶，並開始對租用戶集合執行負載，讓我們看看已部署的一些資源。

1. 在 [Azure 入口網站][](http://portal.azure.com) 中，開啟 **類別目錄-&lt;使用者&gt;**伺服器。 目錄伺服器包含兩個資料庫。 **tenantcatalog**和 **basetenantdb** (複製來建立新租用戶的空 *golden* 資料庫)。

   ![資料庫](./media/sql-database-saas-tutorial/databases.png)

1. 開啟包含租用戶資料庫的 **tenants1-&lt;使用者&gt;** 伺服器。 請注意，每個租用戶資料庫都是 50 eDTU 標準集區中的「彈性標準」資料庫。 另請注意有 _Red Maple Racing_ 資料庫 (您先前佈建的租用戶)。

   ![伺服器](./media/sql-database-saas-tutorial/server.png)

## <a name="monitor-the-pool"></a>監視集區

如果負載產生器已經執行數分鐘，應該以有足夠的資料可供開始查看集區和資料庫的內建監視功能。

1. 瀏覽 [tenants1-&lt;使用者&gt;] 伺服器，然後按一下 [Pool1] 來檢視集區的資源使用率：

   ![監視集區](./media/sql-database-saas-tutorial/monitor-pool.png)

這兩個圖表清楚說明：彈性集區和 SQL Database 非常適合 SaaS 應用程式工作負載。 50 eDTU 集區輕鬆就能支援四個暴增到最多 40 eDTU 的資料庫。 如果每個都是佈建成獨立資料庫，則它們每個都必須是 S2 (50 DTU) 才能支援暴增量，但 4 個獨立 S2 資料庫的成本將近集區的 3 倍。 而且集區還有充足的空餘空間可建立更多資料庫。 在實際情況下，客戶目前在 200 eDTU 集區中執行多達 500 個資料庫。 如需詳細資訊，請參閱[效能監視教學課程](sql-database-saas-tutorial-performance-monitoring.md)。


## <a name="deleting-the-resources-created-with-this-tutorial"></a>刪除此教學課程建立的資源

當您完成 WTP 應用程式的探索及使用之後，請在入口網站中瀏覽到應用程式的資源群組，並將它刪除以停止與此部署相關的所有計費。 如果您已使用任何隨附的教學課程，它們建立的任何資源也將隨應用程式一併刪除。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解：

> [!div class="checklist"]

> * 如何部署 WTP 應用程式
> * 關於組成應用程式的伺服器、集區和資料庫
> * 租用戶會使用「類別目錄」來對應到其資料
> * 如何佈建新租用戶
> * 如何檢視集區使用率以監視租用戶活動
> * 如何刪除範例資源以停止相關計費

現在，請嘗試[佈建和類別目錄教學課程](sql-database-saas-tutorial-provision-and-catalog.md)



## <a name="additional-resources"></a>其他資源

* [建置在初始 Wingtip Tickets Platform (WTP) 應用程式部署上的其他教學課程](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* 若要了解彈性集區，請參閱[什麼是 Azure SQL 彈性集區](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* 若要了解彈性作業，請參閱[*管理相應放大的雲端資料庫*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
* 若要了解多租用戶 SaaS 應用程式，請參閱[*多租用戶 SaaS 應用程式的設計模式*](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)

