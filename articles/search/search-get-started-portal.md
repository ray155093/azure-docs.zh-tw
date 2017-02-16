---
title: "開始使用 Azure 搜尋服務 | Microsoft Docs"
description: "了解如何使用此教學課程的逐步解說和 DocumentDB 範例資料建立第一個 Azure 搜尋服務索引。 這個入口網站式的無程式碼練習會使用匯入資料精靈。"
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 21adc351-69bb-4a39-bc59-598c60c8f958
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 10/03/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 4fc33ba185122496661f7bc49d14f7522d6ee522
ms.openlocfilehash: 02623fc3d663a674e2184380915d651dff5760bc


---
# <a name="get-started-with-azure-search-in-the-portal"></a>開始在入口網站中使用 Azure 搜尋服務
這篇無程式碼的簡介說明如何透過入口網站內建功能來開始使用 Microsoft Azure 搜尋服務。 

本教學課程採用可使用我們的資料和指示輕鬆建立的 [範例 Azure DocumentDB 資料庫](#apdx-sampledata) ，但您也可以將這些步驟套用至 DocumentDB 或 SQL Database 中現有的資料。

> [!NOTE]
> 本入門教學課程需要有 [Azure 訂用帳戶](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)和 [Azure 搜尋服務](search-create-service-portal.md)。 
> 
> 

## <a name="find-your-service"></a>尋找您的服務
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 開啟 Azure 搜尋服務的服務儀表板。 以下提供一些尋找儀表板的方法。
   
   * 在 Jumpbar 中，按一下 [搜尋服務]。 Jumpbar 列出您的訂用帳戶中佈建的每項服務。 如果已定義搜尋服務，您會看到清單中的**搜尋服務**。
   * 在 Jumpbar 中，按一下 [瀏覽]  ，然後在搜尋方塊中輸入 "search" 來產生您訂用帳戶中建立的所有搜尋服務清單。

## <a name="check-for-space"></a>檢查空間
許多客戶開始使用此免費服務。 此版本的限制為三個索引、三個資料來源，以及三個索引子。 開始之前，請先確定您有空間可容納額外的項目。 本逐步解說會建立各一個物件。

## <a name="create-an-index-and-load-data"></a>建立索引和載入資料
搜尋查詢會逐一查看索引  ，其中包含用來最佳化特定搜尋行為的可搜尋資料、中繼資料及建構。 在第一個步驟中，請定義及填入索引。

有數種方法能夠建立索引。 如果您的資料位於 Azure 搜尋服務可以搜耙的存放區 (例如 Azure SQL Database、Azure VM 上的 SQL Server 或 DocumentDB)，您就可以非常輕鬆地使用索引子 建立及填入索引。

為了讓這項工作以入口網站為基礎，我們使用可以透過 [匯入資料精靈] 使用索引子搜耙的 DocumentDB 資料。 

繼續之前，請建立 [範例 DocumentDB 資料庫](#apdx-sampledata) 以搭配本教學課程使用，再回到本節完成下列步驟。

<a id="defineDS"></a>

#### <a name="step-1-define-the-data-source"></a>步驟 1：定義資料來源
1. 在 Azure 搜尋服務儀表板上，按一下命令列中的 [匯入資料]  來啟動可建立及填入索引的精靈。
   
    ![][7]
2. 在精靈中，按一下 [資料來源] > [DocumentDB] > [名稱]，鍵入資料來源的名稱。 資料來源是 Azure 搜尋服務中可以搭配其他索引子使用的連接物件。 建立資料來源後，它就會成為您的服務中可用的「現有資料來源」。
3. 選擇現有的 DocumentDB 帳戶，以及資料庫和集合。 如果您使用我們所提供的範例資料，您的資料來源定義會如下所示：
   
    ![][2]

請注意，我們正略過查詢。 這是因為我們這陣子並未在我們的資料集中實作變更追蹤。 如果您的資料集包含可追蹤何時更新記錄的欄位，您可以設定 Azure 搜尋服務索引子，以對您的索引選擇性更新使用變更追蹤。

按一下 [確定]  來完成精靈的這個步驟。

#### <a name="step-2-define-the-index"></a>步驟 2：定義索引
同樣在精靈中，按一下 [索引]  並查看用來建立 Azure 搜尋服務索引的設計介面。 索引至少需要有名稱和欄位的集合，而其中一個欄位標示為文件索引鍵。 我們正在使用 DocumentDB 資料集，所以精靈會自動偵測欄位，而且索引中會預先載入欄位和資料類型指派。 

  ![][3]

雖已設定欄位和資料類型，但您仍需要指定屬性。 欄位清單上方的核取方塊為索引屬性  ，可控制欄位的使用方式。 

*  表示它會出現在搜尋結果清單中。 例如當欄位僅使用於篩選運算式時，您可以清除此核取方塊，將個別欄位標記為關閉搜尋結果的限制。 
* [可篩選]、[可排序] 和 [可面向化] 判斷欄位是否可以用於篩選、排序或多面向導覽結構。 
*  表示欄位包含在全文檢索搜尋中。 字串通常可以搜尋。 數字欄位和布林值欄位通常會標示為不可搜尋。 

離開此頁面之前，將您索引中的欄位標示為使用下列選項 (可擷取、可搜尋等等)。 大部分的欄位都 [可擷取]。 大部分的字串欄位都 [可搜尋] (您不需要產生可搜尋的索引鍵)。 有些欄位 (像是 orderableOnline、rating 和 tags) 也都 [可篩選]、[可排序] 和 [可面向化]。 

| 欄位 | 類型 | 選項 |
| --- | --- | --- |
| id |Edm.String | |
| albumTitle |Edm.String |可擷取、可搜尋 |
| albumUrl |Edm.String |可擷取、可搜尋 |
| genre |Edm.String |可擷取、可搜尋、可篩選、可排序、可面向化 |
| genreDescription |Edm.String |可擷取、可搜尋 |
| artistName |Edm.String |可擷取、可搜尋 |
| orderableOnline |Edm.Boolean |可擷取、可篩選、可排序、可面向化 |
| tags |Collection(Edm.String) |可擷取、可篩選、可面向化 |
| price |Edm.Double |可擷取、可篩選、可面向化 |
| margin |Edm.Int32 | |
| rating |Edm.Int32 |可擷取、可篩選、可排序、可面向化 |
| inventory |Edm.Int32 |Retrievable |
| lastUpdated |Edm.DateTimeOffset | |

下列螢幕擷取畫面是依上表中的規格建立的索引圖解，以供對照。

 ![][4]

按一下 [確定]  來完成精靈的這個步驟。

#### <a name="step-3-define-the-indexer"></a>步驟 3：定義索引子
同樣在 [匯入資料精靈] 中，按一下 [索引子] > [名稱]，鍵入索引子的名稱，並使用所有其他值的預設值。 此物件定義可執行的程序。 一旦建立此物件，您就可以將其放入週期性排程，但現在請先按一下 [確定] ，立即使用預設選項執行索引子一次。 

匯入資料項目應全部填入並準備就緒。

  ![][5]

若要執行精靈，請按一下 [確定]  開始匯入並關閉精靈。

## <a name="check-progress"></a>檢查進度
若要檢查進度，請回到服務儀表板，向下捲動，然後按兩下 [索引子]  圖格以開啟索引子清單。 您應該會在清單中看到您剛建立的索引子，您應該會看到指出「進行中」或成功的狀態，以及索引編製到 Azure 搜尋服務中的文件數目。

  ![][6]

## <a name="query-the-index"></a>查詢索引
您現在有準備好要查詢的搜尋索引。 

 是入口網站內建的查詢工具。 它會提供搜尋方塊，以便您確認搜尋輸入可傳回您預期的資料。 

1. 按一下命令列上的 [搜尋總管]  。
2. 請注意哪個索引在使用中。 如果這不是您剛才建立的索引，請按一下命令列上的 [變更索引]  以選取您想要的索引。
3. 將搜尋方塊保留空白，然後按一下 [搜尋]  按鈕，以執行可傳回所有文件的萬用字元搜尋。
4. 輸入一些全文檢索搜尋查詢。 您可以檢閱萬用字元搜尋的結果，以熟悉要查詢的演出者、專輯和內容類型。
5. 您可以使用 [本文結尾提供的範例](https://msdn.microsoft.com/library/azure/dn798927.aspx) 以獲得靈感，以及將您的查詢修改成使用可能在索引中找到的搜尋字串，藉此嘗試其他查詢語法。

## <a name="next-steps"></a>後續步驟
在您執行精靈一次之後，您可以返回並檢視或修改個別的元件︰索引、索引子或資料來源。 在索引上不允許進行某些編輯 (例如變更欄位資料類型)，但大部分的屬性和設定都可修改。 若要檢視個別元件，請按一下儀表板上的 [索引]、[索引子] 或 [資料來源] 圖格，以顯示現有物件的清單。

若要深入了解本文提到的其他功能，請造訪下列連結︰

* [索引子](search-indexer-overview.md)
* [建立索引 (包含索引屬性的詳細說明)](https://msdn.microsoft.com/library/azure/dn798941.aspx)
* [搜尋總管](search-explorer.md)
* [搜尋文件 (包含查詢語法範例)](https://msdn.microsoft.com/library/azure/dn798927.aspx)

您可以嘗試此相同的工作流程，並將匯入資料精靈用於 Azure 虛擬機器上的 Azure SQL Database 或 SQL Server 等其他資料來源。

> [!NOTE]
> 最近宣佈索引子支援搜耙 Azure Blob 儲存體，但這項功能處於預覽階段，尚未成為入口網站選項。 若嘗試該索引子，您必須撰寫程式碼。 如需詳細資訊，請參閱 [在 Azure 搜尋服務中編製 Azure Blob 儲存體的索引](search-howto-indexing-azure-blob-storage.md) 。
> <a id="apdx-sampledata"></a>
> 
> 

## <a name="appendix-create-sample-data-in-documentdb"></a>附錄：在 DocumentDB 中建立範例資料
這一節會在 DocumentDB 中建立一個小型資料庫，以便完成此教學課程中的工作。

下列指示可給您一般指引，但並不詳盡。 如果您需要更多有關 DocumentDB 入口網站導覽或工作的協助，可以參考 DocumentDB 文件，但您需要的大部分命令是位於儀表板上方的服務命令列或資料庫刀鋒視窗中。 

  ![][1]

### <a name="create-musicstoredb-for-this-tutorial"></a>建立本教學課程的 musicstoredb
1. [按一下這裡](https://github.com/HeidiSteen/azure-search-get-started-sample-data) 以下載包含音樂市集 JSON 資料檔案的 ZIP 檔案。 我們為此資料集提供 246 個 JSON 文件。
2. 將 DocumentDB 新增至您的訂用帳戶，然後開啟服務儀表板。
3. 按一下 [新增資料庫] 以建立識別碼為 `musicstoredb` 的新資料庫。 所建立的新資料庫會顯示在頁面更下方的資料庫圖格中。
4. 按一下資料庫名稱以開啟資料庫刀鋒視窗。
5. 按一下 [新增集合] 以建立識別碼為 `musicstorecoll` 的集合。
6. 按一下 [文件總管] 。
7. 按一下 [上傳] 。
8. 在 [上傳文件] 中，瀏覽至包含您先前下載之 JSON 檔案的本機資料夾。 以每批 100 個或更少的數量選取 JSON 檔案。
   * 386.json
   * 387.json
   * 。 。 。
   * 486.json
9. 重複執行以取得下一批次檔案，直到您已上傳最後一個 669.json 檔案為止。
10. 按一下 [查詢總管]  以確認資料已上傳，進而符合 Document Explorer 的上傳需求。

若要這麼做，最簡單的方式就是使用預設查詢，但您也可以修改預設查詢，使其選取前 300 個 (此資料集中的項目少於 300 個)。

您應該取回 JSON 輸出 (起始於文件編號 386，結束於文件 669)。 載入資料後，您可以[回到本逐步解說中的步驟](#defineDS)，使用 [匯入資料精靈] 來建置索引。

<!--Image references-->
[1]: ./media/search-get-started-portal/AzureSearch-GetStart-Docdbmenu1.png
[2]: ./media/search-get-started-portal/AzureSearch-GetStart-DataSource.png
[3]: ./media/search-get-started-portal/AzureSearch-GetStart-DefaultIndex.png
[4]: ./media/search-get-started-portal/AzureSearch-GetStart-FinishedIndex.png
[5]: ./media/search-get-started-portal/AzureSearch-GetStart-ImportReady.png
[6]: ./media/search-get-started-portal/AzureSearch-GetStart-IndexerList.png
[7]: ./media/search-get-started-portal/search-data-import-wiz-btn.png



<!--HONumber=Dec16_HO1-->


