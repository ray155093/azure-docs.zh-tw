<properties
 pageTitle="搭配使用 Apache Storm 與 Power BI | Microsoft Azure"
 description="使用來自 HDInsight 中的 Apache Storm 叢集上執行的 C# 拓撲的資料建立 Power BI。"
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="05/27/2016"
 ms.author="larryfr"/>

# 使用 Power BI 視覺化 Apache Storm 拓撲的資料

Power BI 可讓您以視覺化的方式將資料顯示為報告。針對 Storm on HDInsight 使用 Visual Studio 範本，可以輕鬆地從將 Apache Storm on HDInsight 叢集上執行的拓撲中的資料儲存至 SQL Azure，然後使用 Power BI 將資料視覺化。

在本文件中，您將學習如何使用 Power BI 從 Apache Storm 所建立的資料建立報表，並且儲存至 Azure SQL Database。

## 必要條件

- Azure 訂閱。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

* 具備 [Power BI](https://powerbi.com) 存取權的 Azure Active Directory 使用者

* Visual Studio (下列其中一個版本)

    * [Visual Studio 2012](http://www.microsoft.com/download/details.aspx?id=39305) (含 Update 4)

    * Visual Studio 2013 [(含 Update 4)](http://www.microsoft.com/download/details.aspx?id=44921) 或 [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?linkid=517284&clcid=0x409)

    * [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)

* HDInsight Tools for Visual Studio：如需安裝的相關資訊，請參閱[開始使用 HDInsight Tools for Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)。

## 運作方式

此範例包含 C# Storm 拓撲，會隨機產生網際網路資訊服務 (IIS) 記錄資料。然後此資料會寫入至 SQL Database，並且從該處用來在 Power BI 中產生報告。

以下是實作此範例的主要功能的檔案清單。

* **SqlAzureBolt.cs**︰將 Storm 拓撲中產生的資訊寫入 SQL Database。

* **IISLogsTable.sql**︰用來產生儲存資料所在的資料庫的 Transact-SQL 陳述式。

> [AZURE.WARNING] 在 HDInsight 叢集上啟動拓撲之前，您必須在 SQL Database 中建立資料表。

## 下載範例

下載 [HDInsight C# Storm Power BI 範例](https://github.com/Azure-Samples/hdinsight-dotnet-storm-powerbi)。若要下載它，請使用 [git](http://git-scm.com/) 分岔/複製它，或使用**下載**連結下載封存的 .zip。

## 建立資料庫

1. 使用 [SQL Database 教學課程](../sql-database/sql-database-get-started.md)文件中的步驟，建立新的 SQL Database。

2. 遵循[使用 Visual Studio 連接到 SQL Database](../sql-database/sql-database-connect-query.md) 文件中的步驟，連接到資料庫。

4. 以滑鼠右鍵按一下 [物件總管] 中的資料庫，並建立__新查詢__。將下載的專案中包含的 __IISLogsTable.sql__ 檔案的內容，貼上至查詢視窗中，然後使用 Ctrl + Shift + E 來執行查詢。您應該會收到已成功完成命令的訊息。

    完成之後，資料庫中會有名為 __IISLOGS__ 的新資料表。

## 設定範例

1. 從 [Azure 入口網站](https://portal.azure.com)中，選取您的 SQL Database。從 [SQL Database] 刀鋒視窗的 [基本功能] 區段中，選取 [顯示資料庫連接字串]。從顯示的清單中，複製 __ADO.NET (SQL 驗證)__ 資訊。

1. 在 Visual Studio 中開啟範例。從 [方案總管] 開啟 **App.config** 檔案，然後尋找下列項目：

        <add key="SqlAzureConnectionString" value="##TOBEFILLED##" />
    
    將 __##TOBEFILLED##__ 值取代為上一個步驟中複製的資料庫連接字串。將 __{your\_username}__ 和 __{your\_password}__ 取代為資料庫的使用者名稱和密碼。

2. 儲存並關閉檔案。

## 部署範例

1. 從 [方案總管]，以滑鼠右鍵按一下 **StormToSQL** 專案，然後選取 [提交至 Storm on HDInsight]。從 [**Storm 叢集**] 下拉式清單對話方塊選取 HDInsight 叢集。

    > [AZURE.NOTE] [**Storm 叢集**] 下拉式清單可能需要幾秒鐘的時間來填入伺服器名稱。
    >
    > 如果出現提示，請輸入您 Azure 訂閱的登入認證。如果您有多個訂用帳戶，請登入包含 Storm on HDInsight 叢集的訂用帳戶。

2. 順利提交拓撲時，應該會出現叢集的 Storm 拓撲。從清單中選取 SqlAzureWriterTopology 項目，以檢視執行中拓撲的詳細資訊。

    ![已選取拓撲的拓撲](./media/hdinsight-storm-power-bi-topology/topologyview.png)

    您可以使用此檢視在拓撲中查看資訊，或按兩下項目 (例如 SqlAzureBolt)，以查看拓撲中元件的特定資訊。

3. 在拓撲執行幾分鐘之後，返回您用來建立資料庫的 SQL 查詢視窗。以下列程式碼取代現有陳述式。

        select * from iislogs;
    
    使用 Ctrl + Shift + E 來執行查詢，您應該會收到類似下列的結果。
    
        1	2016-05-27 17:57:14.797	255.255.255.255	/bar	GET	200
        2	2016-05-27 17:57:14.843	127.0.0.1	/spam/eggs	POST	500
        3	2016-05-27 17:57:14.850	123.123.123.123	/eggs	DELETE	200
        4	2016-05-27 17:57:14.853	127.0.0.1	/foo	POST	404
        5	2016-05-27 17:57:14.853	10.9.8.7	/bar	GET	200
        6	2016-05-27 17:57:14.857	192.168.1.1	/spam	DELETE	200

    這是從 Storm 拓撲寫入的資料。

## 建立報表

1. 連接到適用於 Power BI 的 [Azure SQL Database 連接器](https://app.powerbi.com/getdata/bigdata/azure-sql-database-with-live-connect)。

2. 在 [資料庫] 內，選取 [Get]。

3. 選取 [Azure SQL Database]，然後選取 [連接]。

4. 輸入資訊以連接到您的 Azure SQL Database。您可以造訪 [Azure 入口網站](https://portal.azure.com)，並且選取您的 SQL Database，以找到這個項目。

    > [AZURE.NOTE] 您也可以從連接對話方塊使用 [啟用進階選項]，設定重新整理間隔和自訂篩選器。

5. 連接之後，您會看到新的資料集具有與您連接的資料庫相同的名稱。選取要開始設計報告的資料集。

3. 從 [欄位] 展開 [IISLOGS] 項目。選取 [URISTEM] 的核取方塊。這會建立新報告，列出資料庫中記錄的 URI 詞幹 (/foo、/bar 等等)。

    ![建立報告](./media/hdinsight-storm-power-bi-topology/createreport.png)

5. 接著，將__方法__拖曳至報告。報告將會更新以列出詞幹和用於 HTTP 要求的對應 HTTP 方法。

    ![新增方法資料](./media/hdinsight-storm-power-bi-topology/uristemandmethod.png)

4. 從 [視覺效果] 資料行中，選取 [欄位] 圖示，然後選取 [值] 區段中 [方法] 旁的向下箭號。從顯示的清單中，選取 [計數]。這會變更報告以列出已存取特定 URI 多少次的計數。

    ![變更為方法的計數](./media/hdinsight-storm-power-bi-topology/count.png)

6. 接下來，選取 [堆疊直條圖] 以變更資訊的顯示方式。

    ![變更堆疊圖表](./media/hdinsight-storm-power-bi-topology/stackedcolumn.png)

7. 一旦您讓報告變成您想要的方式，請使用功能表上的 [儲存] 項目，輸入名稱並儲存報告。

## 停止拓撲

拓撲會繼續執行，直到您將它停止，或刪除 Storm on HDInsight 叢集為止。請執行下列步驟來停止拓撲。

1. 在 Visual Studio 中，返回拓撲檢視器並選取拓撲。

2. 選取 [刪除] 按鈕以停止拓撲。

    ![拓撲摘要上的 [刪除] 按鈕](./media/hdinsight-storm-power-bi-topology/killtopology.png)

## 刪除叢集

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## 後續步驟

在本文件中，您已學會如何將資料從 Storm 拓撲傳送到 SQL Database，然後使用 Power BI 視覺化資料。如需如何使用 Storm on HDInsight 以使用其他 Azure 技術的資訊，請參閱下列項目：

* [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)

<!---HONumber=AcomDC_0601_2016-->