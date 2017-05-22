---
title: "Azure SQL Database 範例多租用戶應用程式 | Microsoft Docs"
description: "Azure SQL Database 範例 Wingtip Tickets (WTP) 應用程式簡介"
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
ms.openlocfilehash: 265eab8104d8af7c510a88dffb9d70a2b3b37631
ms.contentlocale: zh-tw
ms.lasthandoff: 05/12/2017


---
# <a name="introduction-to-the-wingtip-tickets-platform-wtp-sample-saas-application"></a>Wingtip Tickets Platform (WTP) 範例 SaaS 應用程式簡介

Wingtip Tickets Platform (WTP) SaaS 應用程式是一個多租用戶應用程式範例，可示範 SQL Database 的獨特優點。 此應用程式使用租用戶各有資料庫的 SaaS 應用程式模式來維護多租用戶。 WTP 應用程式是設計成用來展示支援 SaaS 案例 (包括 SaaS 設計和管理模式) 的 Azure SQL Database 功能。 為了快速啟動並執行，[五分鐘內就會部署 WTP 應用程式](sql-database-saas-tutorial.md)！

部署 WTP 應用程式之後，請瀏覽在初始部署時建立的[教學課程集合](#sql-database-saas-tutorials)。 每個教學課程都著重於在 SaaS 應用程式中實作的一般工作。 這些工作會依循利用 SQL Database 內建功能的 SaaS 模式來實作。 上述模式包括：佈建新的租用戶、還原租用戶資料庫、對所有租用戶執行分散式查詢，以及將結構描述變更推展至所有租用戶資料庫。 每個教學課程都包含可重複使用的指令碼，以及可大幅簡化理解的詳細說明，並且在您的應用程式中實作相同的 SaaS 管理模式。

WTP 應用程式是頗為完整且吸引人的範例應用程式，請務必著重於與資料層相關的核心 SaaS 模式。 換句話說，著重於資料層，且不要過度分析應用程式本身。 了解這些核心 SaaS 模式的實作方式是在應用程式中實作這些模式的關鍵，同時考慮針對您特定的業務需求進行任何必要的修改。



## <a name="application-architecture"></a>應用程式架構

WTP 應用程式會使用「各租用戶資料庫」模型，並使用 SQL 彈性集區將效率最大化。
使用租用戶目錄進行佈建管理和連線。
整合式應用程式、集區和資料庫的監視與警示 (OMS)。
跨租用戶結構描述和參考資料管理 (彈性資料庫作業)。
跨租用戶查詢、操作分析 (彈性查詢)。
使用異地分散式資料來擴充觸達範圍。
大規模的業務持續性單一租用戶復原 (PITR) DR (異地還原、異地複寫、自動 DR)、租用戶自助式管理 (透過管理 API) PITR 可以從自找的麻煩中復原。

核心 Wingtip 應用程式會使用具有三個範例租用戶的集區，再加上目錄資料庫。

![WTP 架構](media/sql-database-wtp-overview/wtp-architecture.png)


## <a name="sql-database-wtp-saas-tutorials"></a>SQL Database WTP SaaS 教學課程

下列教學課程是以 [Wingtip Tickets Platform SaaS 應用程式範例](sql-database-saas-tutorial.md)的初始部署為基礎：

| 領域 | 說明 | 指令碼位置 |
|:--|:--|:--|
|[佈建和目錄租用戶教學課程](sql-database-saas-tutorial-provision-and-catalog.md)| 佈建新的租用戶並在目錄中註冊它們 | [GitHub 上的指令碼](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Provision%20and%20Catalog) |
|[監視及管理效能教學課程](sql-database-saas-tutorial-performance-monitoring.md)| 監視及管理資料庫和集區效能 | [GitHub 上的指令碼](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Performance%20Monitoring%20and%20Management) |
|[還原單一租用戶教學課程](sql-database-saas-tutorial-restore-single-tenant.md)| 還原租用戶資料庫 | [GitHub 上的指令碼](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Business%20Continuity%20and%20Disaster%20Recovery/RestoreTenant) |
|[管理租用戶結構描述教學課程](sql-database-saas-tutorial-schema-management.md)| 對所有租用戶執行查詢  | [GitHub 上的指令碼](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Schema%20Management) |
|[執行臨機操作分析教學課程](sql-database-saas-tutorial-adhoc-analytics.md) | 建立臨機操作分析資料庫並對所有租用戶執行查詢  | [GitHub 上的指令碼](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Operational%20Analytics/Adhoc%20Analytics) |
|[使用 Log Analytics (OMS) 進行管理教學課程](sql-database-saas-tutorial-log-analytics.md) | 設定及探索 Log Analytics | [GitHub 上的指令碼](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Performance%20Monitoring%20and%20Management/LogAnalytics) |
|[執行租用戶分析教學課程](sql-database-saas-tutorial-tenant-analytics.md) | 設定及執行租用戶分析查詢 | [GitHub 上的指令碼](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Operational%20Analytics/Tenant%20Analytics) |

## <a name="get-the-wingtip-application-scripts"></a>取得 Wingtip 應用程式指令碼

在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) Github 存放庫可取得 Wingtip Tickets 指令碼和應用程式原始程式碼。 指令碼檔案位於 [[Learning Modules] 資料夾](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules)中。 請將 **Learning Modules** 資料夾下載到您的本機電腦，並維持其資料夾結構。

## <a name="working-with-the-wtp-powershell-scripts"></a>使用 WTP PowerShell 指令碼

使用 WTP 應用程式的優點來自於深入了解所提供的指令碼並檢查不同 SaaS 模式的實作方式。

若要檢視所提供的指令碼和模組，以及加速逐步執行進而深入了解它們，請使用 [Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise)。 因為大部分前面加上 Demo- 的指令碼都包含可在執行前修改的變數，所以使用 PowerShell ISE 可簡化這些指令碼處理。

每個 WTP 應用程式部署都有 **UserConfig.psm1** 檔案，其中包含兩個參數，可供設定您在部署期間定義的資源群組和使用者名稱值。 完成部署之後，編輯 **UserConfig.psm1** 模組設定 ResourceGroupName 和 Name 參數。 其他指令碼使用這些值才能順利執行，因此建議在部署完成時設定這些值！



### <a name="execute-scripts-by-pressing-f5"></a>按 F5 執行指令碼

數個指令碼使用 $PSScriptRoot 來允許瀏覽資料夾，而只有在按 **F5** 執行指令碼時才會評估此變數。  醒目提示並執行選取項目 (**F8**) 可能會導致錯誤，所以在執行 WTP 指令碼時按 **F5**。

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>逐步執行指令碼來檢查實作

探索指令碼的實際價值來自於逐步執行它們以了解其作用。 查看第一層 Demo- 指令碼，該指令碼可提供方便讀取的高階工作流程，進而顯示完成各項工作所需的步驟。 更深入探詢個別的呼叫，以查看不同 SaaS 模式的實作詳細資料。

使用及[偵錯 PowerShell 指令碼](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)的祕訣：

* 在 PowerShell ISE 中開啟並設定 demo- 指令碼。
* 按 **F5** 執行或繼續操作。 不建議使用 **F8**，因為在執行指令碼的選取項目時不會評估 $PSScriptRoot。
* 按一下或選取一行，然後按 **F9** 放置中斷點。
* 使用 **F10** 不進入函式或指令碼呼叫。
* 使用 **F11** 逐步執行函式或指令碼呼叫。
* 使用 **Shift + F11** 跳離目前的函式或指令碼呼叫。




## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>使用 SSMS 瀏覽資料庫結構描述並執行 SQL 查詢

使用 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 來連線及瀏覽 WTP 伺服器和資料庫。

WTP 範例應用程式一開始有兩部可連線的 SQL Database 伺服器 - tenants1 伺服器和 catalog 伺服器︰


1. 開啟 SSMS 並連線到 tenants1-*&lt;User&gt;.database.windows.net* 伺服器。
2. 按一下 [連線] > **[資料庫引擎...]**：

   ![目錄伺服器](media/sql-database-wtp-overview/connect.png)

1. 示範認證︰ 登入= developer、密碼 = P@ssword1

   ![connection](media\sql-database-wtp-overview\tenants1-connect.png)

1. 重複步驟 2-3 並連線到 catalog-&lt;User&gt;.database.windows.net 伺服器。

成功連線之後，您會看到這兩部伺服器。 視您佈建的租用戶數量而定，您可能會看到更多或更少的資料庫︰

![物件總管](media/sql-database-wtp-overview/object-explorer.png)



## <a name="next-steps"></a>後續步驟

[部署 Wingtip Tickets SaaS 應用程式範例](sql-database-saas-tutorial.md)
