---
title: "開始使用彈性資料庫工具"
description: "Azure SQL Database 彈性資料庫工具功能的基本解說，包括易於執行的範例應用程式。"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: CarlRabeler
ms.assetid: b6911f8d-2bae-4d04-9fa8-f79a3db7129d
ms.service: sql-database
ms.custom: multiple databases
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: ddove
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: f77c49c47ae7b75a2cbc1cd7521739cafdfba342
ms.lasthandoff: 04/03/2017


---
# <a name="get-started-with-elastic-database-tools"></a>開始使用彈性資料庫工具
本文件將執行範例應用程式介紹開發人員使用經驗。 範例會建立簡易的分區化應用程式，並探討彈性資料庫工具的主要功能。 此範例會示範 [彈性資料庫用戶端程式庫](sql-database-elastic-database-client-library.md)

若要安裝程式庫，請移至 [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)。 安裝的程式庫隨附如下所述的範例應用程式。

## <a name="prerequisites"></a>必要條件
1. 需要具有 C# 的 Visual Studio 2012 或更新版本。 請在 [Visual Studio 下載](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)上下載免費版本。
2. NuGet 2.7 或更新版本。 若要取得最新版本，請參閱 [安裝 NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)

## <a name="download-and-run-the-sample-app"></a>下載及執行範例應用程式
[ **Azure SQL 與彈性資料庫 - 開始使用** ] 範例應用程式會解說使用 Azure SQL 彈性資料庫工具開發分區化應用程式時最重要的開發經驗層面。 範例應用程式著重在[分區對應管理](sql-database-elastic-scale-shard-map-management.md)、[資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)和[多分區查詢](sql-database-elastic-scale-multishard-querying.md)的主要使用案例。 若要下載及執行範例，請遵循下列步驟： 

1. 從 MSDN 下載 [Azure SQL 的彈性 DB 工具：開始使用範例](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6)。 將範例解壓縮至您選擇的位置。
2. 從 **C#** 目錄開啟 **ElasticScaleStarterKit.sln**方案以建立專案。
3. 在範例專案的解決方案中開啟 **app.config** 檔案，然後依照檔案中的指示新增您的 Azure SQL 資料庫伺服器名稱和您的登入資訊 (使用者名稱和密碼)。
4. 建置並執行應用程式。 當系統要求時，請允許 Visual Studio 還原解決方案的 NuGet 套件。 這將會從 NuGet 下載最新版的彈性資料庫用戶端程式庫。
5. 以不同的選項執行，以深入了解用戶端程式庫功能。 請記下應用程式在主控台輸出中採用的步驟，並盡情探索其後的程式碼。
   
    ![progress][4]

恭喜您 - 您已使用彈性資料庫工具，在 Azure SQL Database 上成功建置並執行第一個分區化應用程式。 請使用 Visual Studio 或 SQL Server Management Studio 連接到您的 Azure DB Server，以快速瀏覽範例所建立的分區。 您會看見範例所建立的新範例分區資料庫和分區對應管理員資料庫。

> [!IMPORTANT]
> 建議您一律使用最新版本的 Management Studio 保持與 Microsoft Azure 及 SQL Database 更新同步。 [更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。
> 
> 

### <a name="key-pieces-of-the-code-sample"></a>程式碼範例的主要部分
1. **管理分區和分區對應**：此程式碼會解說如何使用 **ShardManagementUtils.cs** 檔案中的分區、範圍和對應。 您可以在下列連結中找到更多關於此主題的資訊： [分區對應管理](http://go.microsoft.com/?linkid=9862595)。  
2. **資料相依路由**：**DataDependentRoutingSample.cs** 中說明如何將交易路由至正確的分區。 如需詳細資訊，請參閱 [資料相依路由](http://go.microsoft.com/?linkid=9862596)。 
3. **查詢多個分區**：**MultiShardQuerySample.cs** 檔案中說明如何在各個分區間進行查詢。 如需詳細資訊，請參閱 [多分區查詢](http://go.microsoft.com/?linkid=9862597)。
4. **新增空的分區**：反覆新增空分區的作業，由 **CreateShardSample.cs** 檔案中的程式碼所執行。 此主題的詳細資料請見： [分區對應管理](http://go.microsoft.com/?linkid=9862595)。

### <a name="other-elastic-scale-operations"></a>其他 Elastic Scale 作業
1. **分割現有的分區**：分割分區的功能是透過 [分割合併工具] 來提供。 您可以在這裡找到有關此工具的詳細資訊： [分割合併工具概觀](sql-database-elastic-scale-overview-split-and-merge.md)。
2. **合併現有的分區**：分區合併也可使用 [分割合併工具] 來執行。 如需詳細資訊，請參閱： [分割合併工具概觀](sql-database-elastic-scale-overview-split-and-merge.md)。   

## <a name="cost"></a>成本
彈性資料庫工具是免費的。 彈性資料庫工具不會在您的 Azure 使用成本以外收取其他費用。 

例如，範例應用程式會建立新資料庫。 其費用將取決於您所選擇的 Azure SQL DB 資料庫版本，以及您的應用程式的 Azure 使用量。

如需價格資訊，請參閱 [SQL Database 價格詳細資料](https://azure.microsoft.com/pricing/details/sql-database/)。

## <a name="next-steps"></a>後續步驟
如需有關彈性資料庫工具的詳細資訊，請參閱：

* [彈性資料庫工具文件地圖](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/) 
* 程式碼範例： 
  * [彈性資料庫與 Azure SQL - 開始使用](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
  * [彈性資料庫與 Azure SQL - 與 Entity Framework 整合](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [指令碼中心的分區彈性](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* 部落格： [Elastic Scale 公告](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
* Microsoft Virtual Academy：[使用分區化與彈性資料庫用戶端程式庫視訊實作相應放大](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554?l=lWyQhF1fC_6306218965) 
* 第 9 頻道： [Elastic Scale 概觀影片](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* 論壇： [Azure SQL Database 論壇](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* 若要測量效能︰ [分區對應管理員的效能計數器](sql-database-elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png


