---
title: "Azure Cosmos DB 入口網站工具︰文件總管 | Microsoft Docs"
description: "了解「Azure Cosmos DB 文件總管」，這是一個 Azure 入口網站工具，可用來檢視 JSON、編輯、建立及上傳文件。"
keywords: "檢視 json"
services: cosmosdb
author: kirillg
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 029d81b3-6382-4799-a1bd-0dcbccd9968d
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2017
ms.author: kirillg
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 77f8512d9fa47eee25596aa3c407fd6889edbe0e
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="view-edit-create-and-upload-json-documents"></a>檢視、編輯、建立及上傳 JSON文件 

本文提供您可在入口網站中建立、編輯及查詢文件之兩種方法的概觀：[文件總管](#launch-document-explorer)和[資料總管 (預覽)](#data-explorer)。

> [!NOTE]
> 在具有 MongoDB 之通訊協定支援的 Azure Cosmos DB 帳戶上不會啟用「文件總管」。 啟用此功能時，此頁面會加以更新。

<a id="launch-document-explorer"></a>

## <a name="launch-document-explorer-in-the-azure-portal"></a>在 Azure 入口網站中啟動文件總管
1. 在 [Azure 入口網站](https://portal.azure.com)的左側導覽中，按一下 ![Azure Cosmos DB 圖示](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png) [Azure Cosmos DB]。 

    如果看不到 [Azure Cosmos DB]，請按一下底部的 [更多服務]，然後按一下 ![Azure Cosmos DB 圖示](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png) [Azure Cosmos DB]。
2. 選取帳戶名稱。 
3. 在資源功能表中，按一下 [文件總管] 。 
   
    ![[Document Explorer] 命令的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentexplorercommand.png)
   
    在 [文件總管] 刀鋒視窗中，系統會根據您啟動 [文件總管] 時的所在內容，預先填入 [資料庫] 和 [集合] 下拉式清單。 

## <a name="create-a-json-document"></a>建立 JSON 文件
1. [啟動文件總管](#launch-document-explorer)。
2. 在 [文件總管] 刀鋒視窗中，按一下 [建立文件]。 
   
    [文件]  刀鋒視窗中會提供少量的 JSON 程式碼片段。
   
    ![[Document Explorer] 建立文件體驗的螢幕擷取畫面，您可以在其中檢視 JSON 和編輯 JSON](./media/documentdb-view-JSON-document-explorer/createdocument.png)
3. 在 [文件] 刀鋒視窗中，輸入或貼上您想要建立的 JSON 文件內容，然後按一下 [儲存]，以將您的文件認可至 [文件總管] 刀鋒視窗中指定的資料庫和集合。
   
    ![[Document Explorer] 儲存命令的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/savedocument1.png)
   
   > [!NOTE]
   > 如果您未提供 "id" 屬性，則 Document Explorer 會自動新增 id 屬性，並產生一個 GUID 做為識別碼值。
   > 
   > 
   
    如果您已經有來自 JSON 檔案、MongoDB、SQL Server、CSV 檔案、Azure 資料表儲存體、Amazon DynamoDB、HBase 或來自其他 DocumentDB API 集合的資料，便可以使用 DocumentDB API 的[資料移轉工具](documentdb-import-data.md)來快速匯入資料。

## <a name="edit-a-json-document"></a>編輯 JSON 文件
1. [啟動文件總管](#launch-document-explorer)。
2. 若要編輯現有文件，請在 [文件總管] 刀鋒視窗中選取該文件、在 [文件] 刀鋒視窗中進行編輯，然後按一下 [儲存]。
   
    ![用來檢視 JSON 之 [Document Explorer] 編輯文件功能的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/editdocument.png)
   
    如果您正在編輯文件而決定要捨棄目前的這組編輯，只要按一下 [文件] 刀鋒視窗中的 [捨棄]、確認捨棄動作，系統就會重新載入文件的先前狀態。
   
    ![[Document Explorer] 捨棄命令的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/discardedit.png)

## <a name="delete-a-document-from-cosmos-db"></a>從 Cosmos DB 刪除文件
1. [啟動文件總管](#launch-document-explorer)。
2. 在 [文件總管] 中選取文件、按一下 [刪除]，然後確認刪除。 確認後，系統會立即將文件從 Document Explorer 清單中移除。
   
    ![[Document Explorer] 刪除命令的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/deletedocument.png)

## <a name="work-with-json-documents"></a>使用 JSON 文件
Document Explorer 會驗證任何新的或已編輯文件是否包含有效 JSON。  您甚至可藉由移至不正確的區段來檢視 JSON 錯誤，以取得有關驗證錯誤的詳細資訊。

![JSON 反白顯示無效之 [Document Explorer] 的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/invalidjson1.png)

此外，Document Explorer 會防止您儲存包含無效 JSON 內容的文件。

![JSON 無效儲存錯誤之 [Document Explorer] 的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/invalidjson2.png)

最後，Document Explorer 可讓您輕鬆地檢視目前載入之文件的系統屬性，您只需按一下 [屬性]  命令即可。

![[Document Explorer] 文件屬性檢視的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentproperties.png)

> [!NOTE]
> 時間戳記 (_ts) 屬性在內部會以 Epoch 時間表示，但是 Document Explorer 會以一般人可判讀的 GMT 格式顯示此值。
> 
> 

## <a name="filter-documents"></a>篩選文件
[文件總管] 支援許多導覽選項以及進階設定。

根據預設，Document Explorer 會在選取的集合中，依照建立日期 (最早到最晚) 載入前 100 個文件。  您可以選取 [Document Explorer] 刀鋒視窗底部的 [載入更多]  選項，載入其他的文件 (每批 100 個文件)。 您可以透過 [篩選]  命令來選擇要載入哪些文件。

1. [啟動文件總管](#launch-document-explorer)。
2. 在 [文件總管] 刀鋒視窗的頂端，按一下 [篩選]。  
   
    ![[Document Explorer 篩選設定] 的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentexplorerfiltersettings.png)
3. 篩選設定會出現在命令列下方。 在篩選設定中，提供 WHERE 子句及/或 ORDER BY 子句，然後按一下 [篩選] 。
   
   ![[Document Explorer 設定] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentexplorerfiltersettings2.png)
   
   Document Explorer 會以符合篩選查詢的文件自動重新整理結果。 若要深入了解 DocumentDB API SQL 文法，請參閱 [SQL 查詢和 SQL 語法](documentdb-sql-query.md)一文，或列印一份 [SQL 查詢速查表](documentdb-sql-query-cheat-sheet.md)。
   
   您可以使用 [資料庫] 和 [集合] 下拉式清單方塊輕鬆地變更目前從中檢視文件的集合，而無需關閉並重新啟動「文件總管」。  
   
   Document Explorer 還支援根據 id 屬性篩選目前載入的文件集。  您只需在 [依識別碼篩選文件] 方塊中輸入即可。
   
   ![反白顯示篩選條件的 [Document Explorer] 螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentexplorerfilter.png)
   
   Document Explorer 清單中的結果便會根據您所提供的準則進行篩選。
   
   ![含篩選結果的 [Document Explorer] 螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentexplorerfilterresults.png)
   
   > [!IMPORTANT]
   > 「文件總管」篩選功能只會從「目前」載入的一組文件進行篩選，而不會針對目前選取的集合執行查詢。
   > 
   > 
4. 若要重新整理 Document Explorer 所載入的文件清單，請按一下刀鋒視窗頂端的 [重新整理]  。
   
    ![[Document Explorer] 重新整理命令的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentexplorerrefresh.png)

## <a name="bulk-add-documents"></a>大量新增文件
Document Explorer 支援大量擷取一或多個現有 JSON 文件，每個上傳作業最多 100 個 JSON 檔案。  

1. [啟動文件總管](#launch-document-explorer)。
2. 若要開始上傳程序，請按一下 [上傳文件] 。
   
    ![[Document Explorer] 大量擷取功能的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/uploaddocument1.png)
   
    [上傳文件]  刀鋒視窗隨即開啟。 
3. 按一下 [瀏覽] 按鈕以開啟檔案總管視窗、選取要上傳的一或多個 JSON 文件，然後按一下 [開啟] 。
   
    ![[Document Explorer] 大量擷取程序的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/uploaddocument2.png)
   
   > [!NOTE]
   > Document Explorer 的每個個別上傳作業目前支援多達 100 個 JSON 文件。
   > 
   > 
4. 對您的選取項目感到滿意後，請按一下 [上傳]  按鈕。  文件會自動新增至 Document Explorer 方格，且上傳結果會顯示為作業進度。 系統會針對個別檔案回報匯入失敗。
   
    ![[Document Explorer] 大量擷取結果的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/uploaddocument3.png)
5. 作業完成後，您可以另外選取多達 100 個文件進行上傳。

<a id="data-explorer"></a>
## <a name="create-a-document-by-using-data-explorer-preview"></a>使用資料總管 (預覽) 來建立文件

另一個在入口網站中建立、編輯及查詢文件的方法是使用 [資料總管]。 若要開啟 [資料總管]，請按一下入口網站中導覽列上的 [資料總管 (預覽)]，接著展開您的資料庫名稱、展開您的集合名稱、按一下 [文件]，然後按一下 [新增文件]，如以下螢幕擷取畫面所示。

![顯示入口網站中 [新增集合] 按鈕的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/azure-documentdb-data-explorer.png)

## <a name="work-with-json-documents-outside-the-portal"></a>在入口網站外部使用 JSON 文件
Azure 入口網站中的「文件總管」只是一種使用 Cosmos DB 中文件的方法。 您也可以使用 [REST API](https://msdn.microsoft.com/library/azure/mt489082.aspx) 或[用戶端 SDK](documentdb-sdk-dotnet.md) 來處理文件。 如需範例程式碼，請參閱 [.NET SDK 文件範例](documentdb-dotnet-samples.md#document-examples)和 [Node.js SDK 文件範例](documentdb-nodejs-samples.md#document-examples)。

如果您需要從其他來源 (JSON 檔案、MongoDB、SQL Server、CSV 檔案、Azure 表格儲存體、Amazon DynamoDB 或 HBase) 匯入或移轉檔案，則可以使用 Cosmos DB [資料移轉工具](documentdb-import-data.md)將您的資料快速匯入到 Cosmos DB。

## <a name="troubleshoot"></a>疑難排解
**徵兆**︰「文件總管」傳回 [找不到任何文件]。

**解決方案**︰確定您已選取正確的訂用帳戶、資料庫，以及已插入文件的集合。 此外，檢查以確定您是在輸送量配額內運作。 如果您是在最大輸送量層級上運作並受到節流處理，請降低應用程式使用量，以便在集合的最大輸送量配額下運作。

**說明**︰此入口網站是一個應用程式，就像所有其他應用程式一樣，可呼叫您的 Cosmos DB 資料庫和集合。 如果您的要求目前因為個別應用程式所進行的呼叫而受到節流處理，入口網站也可能會受到節流處理，因而導致資源不會在入口網站中顯示。 若要解決此問題，請先解決導致出現高輸送使用量的原因，然後重新整理入口網站刀鋒視窗。 如需有關如何測量及降低輸送使用量的資訊，請參閱[效能祕訣](documentdb-performance-tips.md)一文的[輸送量](documentdb-performance-tips.md#throughput)一節。

## <a name="next-steps"></a>後續步驟
若要深入了解「文件總管」中支援的 DocumentDB API SQL 文法，請參閱 [SQL 查詢和 SQL 語法](documentdb-sql-query.md)一文，或列印 [SQL 查詢速查表](documentdb-sql-query-cheat-sheet.md)。


