---
title: "搭配使用 Apache Storm 與 Power BI | Microsoft Docs"
description: "使用來自 HDInsight 中的 Apache Storm 叢集上執行的 C# 拓撲的資料建立 Power BI。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 36fe3b9c-5232-4464-8d75-95403b6da7a1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: bedb6816e4f203687529e95ffa24688e86b5a3cb
ms.lasthandoff: 04/12/2017


---
# <a name="use-power-bi-to-visualize-data-from-an-apache-storm-topology"></a>使用 Power BI 視覺化 Apache Storm 拓撲的資料

Power BI 可讓您以視覺化的方式將資料顯示為報告。 本文件提供如何使用 Apache Storm on HDInsight 為 Power BI 產生資料的範例。

> [!NOTE]
> 雖然本文件中的步驟依賴 Windows 開發環境與 Visual Studio，但已編譯的專案可以提交到以 Linux 或 Windows 為基礎的 HDInsight 叢集。 只有在 2016/10/28 之後建立的以 Linux 為基礎的叢集可支援 SCP.NET 拓撲。
>
> 若要搭配使用 C# 拓撲與以 Linux 為基礎的叢集，請將專案使用的 Microsoft.SCP.Net.SDK NuGet 套件，更新為 0.10.0.6 版或更新版本。 套件版本也必須符合 HDInsight 上安裝的 Storm 主要版本。 例如，Storm on HDInsight 3.3 和 3.4 版使用 Storm 0.10.x 版，而 HDInsight 3.5 使用 Storm 1.0.x。
>
> 在以 Linux 為基礎之叢集上的 C# 拓撲必須使用 .NET 4.5，並使用 Mono 以在 HDInsight 叢集上執行。 大多能正常運作。 不過您應該查看 [Mono 相容性](http://www.mono-project.com/docs/about-mono/compatibility/)文件，以了解是否可能有不相容之處。
>
> 如需此專案的 Java 版本 (可在以 Linux 或 Windows 為基礎的 HDInsight 上運作)，請參閱[使用 Storm on HDInsight 處理 Azure 事件中樞的事件 (Java)](hdinsight-storm-develop-java-event-hub-topology.md)。

## <a name="prerequisites"></a>必要條件

* 具備 [Power BI](https://powerbi.com) 存取權的 Azure Active Directory 使用者。
* HDInsight 叢集。 如需詳細資訊，請參閱[開始使用 Storm on HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md)。

  > [!IMPORTANT]
  > Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 取代](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date)。

* Visual Studio (下列其中一個版本)

  * [Visual Studio 2012](http://www.microsoft.com/download/details.aspx?id=39305)
  * Visual Studio 2013 [(含 Update 4)](http://www.microsoft.com/download/details.aspx?id=44921) 或 [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?linkid=517284&clcid=0x409)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
  * Visual Studio 2017 (任何版本)

* HDInsight Tools for Visual Studio：如需安裝的相關資訊，請參閱 [開始使用 HDInsight Tools for Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) 。

## <a name="how-it-works"></a>運作方式

此範例包含 C# Storm 拓撲，會隨機產生網際網路資訊服務 (IIS) 記錄資料。 然後此資料會寫入至 SQL Database，並且從該處用來在 Power BI 中產生報告。

下列檔案會實作此範例的主要功能︰

* **SqlAzureBolt.cs**︰將 Storm 拓撲中產生的資訊寫入 SQL Database。
* **IISLogsTable.sql**︰用來產生儲存資料所在的資料庫的 Transact-SQL 陳述式。

> [!WARNING]
> 在 HDInsight 叢集上啟動拓撲之前，先於 SQL Database 中建立資料表。

## <a name="download-the-example"></a>下載範例

下載 [HDInsight C# Storm Power BI 範例](https://github.com/Azure-Samples/hdinsight-dotnet-storm-powerbi)。 若要下載它，請使用 [git](http://git-scm.com/)分岔/複製它，或使用 **下載** 連結下載封存的 .zip。

## <a name="create-a-database"></a>建立資料庫

1. 若要建立 SQL Database，請使用 [SQL Database 教學課程](../sql-database/sql-database-get-started.md)文件中的步驟。

2. 遵循[使用 Visual Studio 連線至 SQL Database](../sql-database/sql-database-connect-query.md)文件中的步驟，連線至資料庫。

3. 在 [物件總管] 中，以滑鼠右鍵按一下資料庫，然後選取 [新增查詢]。 將下載的專案中包含的 **IISLogsTable.sql** 檔案的內容，貼上至查詢視窗中，然後使用 Ctrl + Shift + E 來執行查詢。 您應該會收到已成功完成命令的訊息。

## <a name="configure-the-sample"></a>設定範例

1. 從 [Azure 入口網站](https://portal.azure.com)中，選取您的 SQL Database。 從 [SQL Database] 刀鋒視窗的 [基本功能] 區段中，選取 [顯示資料庫連接字串]。 從顯示的清單中，複製 **ADO.NET (SQL 驗證)** 資訊。

2. 在 Visual Studio 中開啟範例。 從 [方案總管] 開啟 **App.config** 檔案，然後尋找下列項目：

        <add key="SqlAzureConnectionString" value="##TOBEFILLED##" />

    將 **##TOBEFILLED##** 值取代為上一個步驟中複製的資料庫連接字串。 將 **{your\_username}** 和 **{your\_password}** 取代為資料庫的使用者名稱和密碼。

3. 儲存並關閉檔案。

## <a name="deploy-the-sample"></a>部署範例

1. 從 [方案總管]，以滑鼠右鍵按一下 **StormToSQL** 專案，然後選取 [提交至 Storm on HDInsight]。 從 [Storm 叢集] 下拉式清單對話方塊選取 HDInsight 叢集。

   > [!NOTE]
   > [ **Storm 叢集** ] 下拉式清單可能需要幾秒鐘的時間來填入伺服器名稱。
   >
   > 如果出現提示，請輸入您 Azure 訂閱的登入認證。 如果您有多個訂用帳戶，請登入包含 Storm on HDInsight 叢集的訂用帳戶。

2. 提交拓撲之後，[拓撲檢視器] 便會隨即出現。 若要檢視此拓撲，請從清單中選取 SqlAzureWriterTopology 項目。

    ![已選取拓撲的拓撲](./media/hdinsight-storm-power-bi-topology/topologyview.png)

    您可以使用此檢視在拓撲中查看資訊，或按兩下項目 (例如 SqlAzureBolt)，以查看拓撲中元件的特定資訊。

3. 在拓撲執行幾分鐘之後，返回您用來建立資料庫的 SQL 查詢視窗。 以下列查詢取代現有陳述式：

        select * from iislogs;

    使用 Ctrl + Shift + E 來執行查詢，您應該會收到類似下列的結果：

        1    2016-05-27 17:57:14.797    255.255.255.255    /bar    GET    200
        2    2016-05-27 17:57:14.843    127.0.0.1    /spam/eggs    POST    500
        3    2016-05-27 17:57:14.850    123.123.123.123    /eggs    DELETE    200
        4    2016-05-27 17:57:14.853    127.0.0.1    /foo    POST    404
        5    2016-05-27 17:57:14.853    10.9.8.7    /bar    GET    200
        6    2016-05-27 17:57:14.857    192.168.1.1    /spam    DELETE    200

    這是從 Storm 拓撲寫入的資料。

## <a name="create-a-report"></a>建立報表

1. 連接到適用於 Power BI 的 [Azure SQL Database 連接器](https://app.powerbi.com/getdata/bigdata/azure-sql-database-with-live-connect) 。

2. 在 [資料庫] 內，選取 [Get]。

3. 選取 [Azure SQL Database]，然後選取 [連接]。

4. 輸入資訊以連接到您的 Azure SQL Database。 您可以造訪 [Azure 入口網站](https://portal.azure.com)，並且選取您的 SQL Database，以找到此資訊。

   > [!NOTE]
   > 您也可以從連接對話方塊使用 [啟用進階選項]，設定重新整理間隔和自訂篩選器。

5. 連接之後，您會看到新的資料集具有與您連接的資料庫相同的名稱。 選取要開始設計報告的資料集。

6. 從 [欄位] 展開 [IISLOGS] 項目。 選取 [URISTEM] 的核取方塊。 這會建立報告，列出資料庫中記錄的 URI 詞幹 (/foo、/bar 等等)。

    ![建立報告](./media/hdinsight-storm-power-bi-topology/createreport.png)

7. 接著，將 **方法** 拖曳至報告。 報告將會更新以列出詞幹和用於 HTTP 要求的對應 HTTP 方法。

    ![新增方法資料](./media/hdinsight-storm-power-bi-topology/uristemandmethod.png)

8. 從 [視覺效果] 資料行中，選取 [欄位] 圖示，然後選取 [值] 區段中 [方法] 旁的向下箭號。 從顯示的清單中，選取 [計數] 。 這會變更報告以列出已存取特定 URI 多少次的計數。

    ![變更為方法的計數](./media/hdinsight-storm-power-bi-topology/count.png)

9. 接下來，選取 [堆疊直條圖]  以變更資訊的顯示方式。

    ![變更堆疊圖表](./media/hdinsight-storm-power-bi-topology/stackedcolumn.png)

10. 若要儲存報告，請選取 [儲存] 並輸入報告的名稱。

## <a name="stop-the-topology"></a>停止拓撲

拓撲會繼續執行，直到您將它停止，或刪除 Storm on HDInsight 叢集為止。 請執行下列步驟來停止拓撲。

1. 在 Visual Studio 中，返回拓撲檢視器並選取拓撲。

2. 選取 [刪除]  按鈕以停止拓撲。

    ![拓撲摘要上的刪除按鈕](./media/hdinsight-storm-power-bi-topology/killtopology.png)

## <a name="delete-your-cluster"></a>刪除叢集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>後續步驟

在本文件中，您已學會如何將資料從 Storm 拓撲傳送到 SQL Database，然後使用 Power BI 視覺化資料。 如需如何使用 Storm on HDInsight 以使用其他 Azure 技術的資訊，請參閱下列項目：

* [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)

