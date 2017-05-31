---
title: "Azure Cosmos DB 入口網站工具︰查詢總管 | Microsoft Docs"
description: "深入了解 Azure Cosmos DB 查詢總管，它是 Azure 入口網站中的 SQL 查詢編輯器，可針對 Azure Cosmos DB 集合撰寫和執行 SQL 查詢。"
keywords: "撰寫 sql 查詢, sql 查詢編輯器"
services: cosmosdb
author: kirillg
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: ac378240-b11f-4522-ae9f-09da3a6f9c16
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: kirillg
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 77389c6d0779a08b8d717076fc5678d3d7fb3b0c
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="write-edit-and-run-sql-queries-for-azure-cosmos-db-using-query-explorer-in-the-azure-portal"></a>在 Azure 入口網站中使用查詢總管針對 Azure Cosmos DB 撰寫、編輯和執行 SQL 查詢
本篇文章提供了 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) 查詢總管的概述，它是一項 Azure 入口網站工具，可讓您針對 [DocumentDB 集合](documentdb-create-collection.md)撰寫、編輯和執行 SQL 查詢。

1. 在 [Azure 入口網站](https://portal.azure.com)的左側導覽中，按一下 ![Azure Cosmos DB 圖示](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png) [Azure Cosmos DB]。 

    如果看不到 [Azure Cosmos DB]，請按一下底部的 [更多服務]，然後按一下 ![Azure Cosmos DB 圖示](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png) [Azure Cosmos DB]。
2. 在資源功能表中，按一下 [查詢總管] 。 
   
    ![已反白顯示 [查詢總管] 的 Azure 入口網站的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryexplorercommand.png)
3. 在 [查詢總管] 刀鋒視窗中，從下拉式清單中選取要查詢的 [資料庫] 和 [集合]，然後輸入要執行的查詢。 
   
    [資料庫] 和 [集合] 下拉式清單會根據您在其中啟動 [查詢總管] 的內容預先填入。 
   
    系統會提供 `SELECT TOP 100 * FROM c` 的預設查詢。  您可以使用 [SQL 查詢速查表](documentdb-sql-query-cheat-sheet.md)或 [SQL 查詢和 SQL 語法](documentdb-sql-query.md)一文中描述的 SQL 查詢語言，接受預設查詢或建構自己的查詢。
   
    按一下 [執行查詢]  以檢視結果。
   
    ![在 [查詢總管] (SQL 查詢編輯器) 中撰寫 SQL 查詢的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryexplorerinitial.png)
4. [結果]  刀鋒視窗會顯示查詢的輸出。 
   
    ![在 [查詢總管] 中撰寫 SQL 查詢之結果的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryresults1.png)

## <a name="work-with-results"></a>處理結果
根據預設，[查詢總管] 會傳回結果，以 100 為一組。  如果您的查詢產生的結果超過 100 個，您只需使用 [下一頁] 和 [上一頁] 命令即可瀏覽結果集。

![[查詢總管] 分頁支援的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryresultspagination.png)

對於成功的查詢，[資訊] 窗格會包含如要求費用、查詢所進行的來回行程數目、目前顯示的結果集，以及是否有更多結果等資訊，您可以透過先前所述的 [下一頁] 命令進行存取。

![[查詢總管] 查詢資訊的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryinformation.png)

## <a name="use-multiple-queries"></a>使用多個佇列
如果您使用多個查詢，而且想要在其間快速切換，您可以在 [查詢總管] 刀鋒視窗的查詢文字方塊中輸入所有查詢，將您要執行的查詢反白，然後按一下 [執行查詢] 以檢視結果。

![在 [查詢總管] (SQL 查詢編輯器) 中撰寫多個 SQL 查詢，然後反白顯示並執行個別查詢的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryexplorerhighlightandrun.png)

## <a name="add-queries-from-a-file-into-the-sql-query-editor"></a>將查詢從檔案加入至 SQL 查詢編輯器
您可以使用 [載入檔案]  命令載入現有檔案的內容。

![顯示如何使用載入檔案將 SQL 查詢從檔案載入 [查詢總管] 的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/loadqueryfile.png)

## <a name="troubleshoot"></a>疑難排解
如果查詢完成但發生錯誤，則 [查詢總管] 會顯示可協助進行疑難排解工作的錯誤清單。

![[查詢總管] 查詢錯誤的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryerror.png)

## <a name="run-documentdb-api-sql-queries-outside-the-portal"></a>在入口網站外部執行 DocumentDB API SQL 查詢
Azure 入口網站中的 [查詢總管] 只是一種對 Cosmos DB 執行 SQL 查詢的方法。 您也可以使用 [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 或[用戶端 SDK](documentdb-sdk-dotnet.md) 執行 SQL 查詢。 如需使用上述其他方法的詳細資訊，請參閱 [執行 SQL 查詢](documentdb-sql-query.md#ExecutingSqlQueries)

## <a name="next-steps"></a>後續步驟
若要深入了解 [查詢總管] 中支援的 DocumentDB API SQL 文法，請參閱 [SQL 查詢和 SQL 語法](documentdb-sql-query.md)一文，或列印 [SQL 查詢速查表](documentdb-sql-query-cheat-sheet.md)。
您也可以享受 [Query Playground](https://www.documentdb.com/sql/demo) 體驗，在其中使用範例資料集在線上測試查詢。


