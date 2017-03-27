---
title: "使用指令碼動作在 Azure HDInsight  上安裝 Spark 叢集的 Zeppelin Notebook | Microsoft Docs"
description: "如何在 HDInsight Linux 上對 Spark 叢集安裝和使用 Zeppelin Notebook 的逐步指示。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: cb276220-fb02-49e2-ac55-434fcb759629
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: eadf0611ca46a975c364a1b073828c6c3faf5f77
ms.lasthandoff: 01/24/2017


---
# <a name="install-zeppelin-notebooks-for-apache-spark-cluster-on-hdinsight"></a>為 HDInsight 上的 Apache Spark 叢集安裝 Zeppelin Notebook

了解如何在 Apache Spark 叢集上安裝 Zeppelin Notebook，以及如何使用 Zeppelin Notebook 來執行 Spark 作業。

> [!IMPORTANT]
> 如果您已在 HDInsight 3.5 佈建 Spark 1.6 叢集， 您可以使用[在 HDInsight Linux 上搭配使用 Zeppelin Notebook 和 Apache Spark 叢集](hdinsight-apache-spark-zeppelin-notebook.md)的指示來存取預設 Zeppelin Notebook。 如果您想要在 HDInsight 叢集版本 3.3、3.4 上或在 HDInsight 3.5 上的 Spark 2.0 使用 Zeppelin，您必須遵循本文中的指示來安裝 Zeppelin。
>
>

**必要條件：**

* 開始進行本教學課程之前，您必須擁有 Azure 訂用帳戶。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱 [在 Azure HDInsight 中建立 Apache Spark 叢集](hdinsight-apache-spark-jupyter-spark-sql.md)。
* SSH 用戶端。 若為 Linux 和 Unix 發佈或 Macintosh OS X， `ssh` 命令會隨作業系統提供。 若為 Windows，我們建議使用 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

  > [!NOTE]
  > 如果您想要使用 `ssh` 或 PuTTY 以外的 SSH 用戶端，請參考您用戶端的說明文件，了解如何建立 SSH 通道。
  >
  >
* 可以設定為使用 SOCKS Proxy 的網頁瀏覽器。
* **(選擇性)**：如 [FoxyProxy](http://getfoxyproxy.org/,) 之類的外掛程式，其可套用的規則是只將特定要求透過通道進行路由傳送。

  > [!WARNING]
  > 若無 FoxyProxy 之類的外掛程式，所有透過瀏覽器建立的要求可能都會透過通道進行路由傳送。 這會導致瀏覽器中的網頁載入速度較慢。
  >
  >

## <a name="install-zeppelin-on-a-spark-cluster"></a>在 Spark 叢集上安裝 Zeppelin
您可以使用指令碼動作在 Spark 叢集上安裝 Zeppelin。 指令碼動作會使用自訂指令碼在叢集上安裝不是預設可用的元件。 您可以使用自訂指令碼從 Azure 入口網站安裝 Zeppelin，使用 HDInsight.NET SDK 或 Azure PowerShell 都可以。 您可以使用指令碼，在叢集建立期間安裝 Zeppelin，或在叢集已啟動並執行之後加以安裝。 下列各節中的連結提供如何執行這項操作的指示。

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站
如需有關如何使用 Azure 入口網站來執行指令碼動作以安裝 Zeppelin 的指示，請參閱 [使用指令碼動作來自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)。 您必須對該文中的指示進行一些變更。

* 您必須使用指令碼來安裝 Zeppelin。 用以在 HDInsight 上的 Spark 叢集上安裝 Zeppelin 的自訂指令碼可從下列連結取得：

  * Spark 1.6.0 叢集 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
  * Spark 1.5.2 叢集 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`
* 您只須在前端節點上執行此指令碼動作。
* 此指令碼不需要任何參數。

### <a name="using-hdinsight-net-sdk"></a>使用 HDInsight .NET SDK
如需有關如何使用 HDInsight .NET SDK 來執行指令碼動作以安裝 Zeppelin 的指示，請參閱 [使用指令碼動作來自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)。 您必須對該文中的指示進行一些變更。

* 您必須使用指令碼來安裝 Zeppelin。 用以在 HDInsight 上的 Spark 叢集上安裝 Zeppelin 的自訂指令碼可從下列連結取得：

  * Spark 1.6.0 叢集 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
  * Spark 1.5.2 叢集 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`
* 此指令碼不需要任何參數。
* 將您要建立的叢集類型設為 Spark。

### <a name="using-azure-powershell"></a>使用 Azure PowerShell
使用下列 PowerShell 程式碼片段，在已安裝 Zeppelin 的 HDInsight Linux 上建立 Spark 叢集。 您必須更新以下的 PowerShell 片段，加入對應之自訂指令碼的連結，連結依您擁有的 Spark 叢集版本而異。

* Spark 1.6.0 叢集 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
* Spark 1.5.2 叢集 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    Login-AzureRMAccount

    # PROVIDE VALUES FOR THE VARIABLES
    $clusterAdminUsername="admin"
    $clusterAdminPassword="<<password>>"
    $clusterSshUsername="adminssh"
    $clusterSshPassword="<<password>>"
    $clusterName="<<clustername>>"
    $clusterContainerName=$clusterName
    $resourceGroupName="<<resourceGroupName>>"
    $location="<<region>>"
    $storage1Name="<<storagename>>"
    $storage1Key="<<storagekey>>"
    $subscriptionId="<<subscriptionId>>"

    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    $passwordAsSecureString=ConvertTo-SecureString $clusterAdminPassword -AsPlainText -Force
    $clusterCredential=New-Object System.Management.Automation.PSCredential ($clusterAdminUsername, $passwordAsSecureString)
    $passwordAsSecureString=ConvertTo-SecureString $clusterSshPassword -AsPlainText -Force
    $clusterSshCredential=New-Object System.Management.Automation.PSCredential ($clusterSshUsername, $passwordAsSecureString)

    $azureHDInsightConfigs= New-AzureRmHDInsightClusterConfig -ClusterType Spark
    $azureHDInsightConfigs.DefaultStorageAccountKey = $storage1Key
    $azureHDInsightConfigs.DefaultStorageAccountName = "$storage1Name.blob.core.windows.net"

    Add-AzureRMHDInsightScriptAction -Config $azureHDInsightConfigs -Name "Install Zeppelin" -NodeType HeadNode -Parameters "void" -Uri "https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh"

    New-AzureRMHDInsightCluster -Config $azureHDInsightConfigs -OSType Linux -HeadNodeSize "Standard_D12" -WorkerNodeSize "Standard_D12" -ClusterSizeInNodes 2 -Location $location -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $clusterCredential -DefaultStorageContainer $clusterContainerName -SshCredential $clusterSshCredential -Version "3.3"

## <a name="access-the-zeppelin-notebook"></a>存取 Zeppelin Notebook

使用指令碼動作成功安裝 Zeppelin 之後，您可以遵循下列步驟，在 Spark 叢集上存取 Zeppelin Notebook。 在本節中，您將了解如何執行 %sql 和 %hive 陳述式。

1. 從網頁瀏覽器開啟下列端點：

        https://CLUSTERNAME.azurehdinsight.net/zeppelin

   
2. 建立新的 Notebook。 按一下標頭窗格中的 [Notebook]，然後按一下 [建立新 Note]。

    ![建立新的 Zeppelin Notebook](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.createnewnote.png "建立新的 Zeppelin Notebook")

    在同一個頁面的 [Notebook] 標頭下方，您應該會看到名稱開頭為 **Note XXXXXXXXX** 的新 Notebook。 按一下新的 Notebook。
3. 在新 Notebook 的網頁上按一下標題，需要的話可以變更 Notebook 的名稱。 按下 ENTER 以儲存名稱變更。 此外，請確定 Notebook 標頭的右上角顯示 [已連線]  狀態。

    ![Zeppelin Notebook 狀態](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.newnote.connected.png "Zeppelin Notebook 狀態")

### <a name="run-sql-statements"></a>執行 SQL 陳述式
1. 將範例資料載入暫存資料表。 當您在 HDInsight 中建立 Spark 叢集時，系統會將範例資料檔案 **hvac.csv** 複製到相關聯的儲存體帳戶中 (位於 **\HdiSamples\SensorSampleData\hvac**)。

    將以下程式碼片段貼入新 Notebook 中預設建立的空白段落。

        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0),
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()

        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")

    按下 **SHIFT + ENTER**，或是按一下 [播放] 按鈕來讓段落執行程式碼片段。 段落右上角的狀態應該會從「準備就緒」逐一轉變成「擱置」、「執行中」及「已完成」。 輸出會顯示在同一個段落的底部。 螢幕擷取畫面如下所示：

    ![從未經處理資料建立暫存資料表](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.loaddDataintotable.png "從未經處理資料建立暫存資料表")

    您也可以為每個段落提供標題。 按一下右下角的 [設定] 圖示，然後按一下 [顯示標題]。
2. 現在，您可以針對 **hvac** 資料表執行 Spark SQL 陳述式。 將以下查詢貼入新段落。 此查詢會擷取建築物識別碼，以及在指定日期當天每棟建築物之目標溫度與實際溫度間的差異。 按下 **SHIFT + ENTER**。

        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date
        from hvac
        where date = "6/1/13"

    開頭的 **%Sql** 陳述式會告訴 Notebook 使用 Spark SQL 解譯器。 您可以在 Notebook 標頭的 [解譯器]  索引標籤中，查看已定義的解譯器。

    以下螢幕擷取畫面顯示輸出。

    ![使用 Notebook 執行 Spark SQL 陳述式](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery1.png "使用 Notebook 執行 Spark SQL 陳述式")

     按一下顯示選項 (以矩形反白顯示) 以針對相同輸出切換不同的表示法。 按一下 [設定] 以選擇構成輸出中索引鍵和值的項目。 在上方的螢幕擷取畫面中，索引鍵為 **buildingID**，而值為 **temp_diff** 的平均值。
3. 您也可以在查詢中使用變數來執行 Spark SQL 陳述式。 下一個程式碼片段示範如何利用您想要查詢的可能值，來定義查詢中的變數 **Temp**。 當您第一次執行查詢時，下拉式清單會自動填入您指定的變數值。

        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
        from hvac
        where targettemp > "${Temp = 65,65|75|85}"

    將此程式碼片段貼到新的段落中，然後按下 **SHIFT + ENTER**。 以下螢幕擷取畫面顯示輸出。

    ![使用 Notebook 執行 Spark SQL 陳述式](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery2.png "使用 Notebook 執行 Spark SQL 陳述式")

    對於後續的查詢，您可以從下拉式清單選取新的值，然後再次執行查詢。 按一下 [設定] 以選擇構成輸出中索引鍵和值的項目。 上述螢幕擷取畫面使用 **buildingID** 做為索引鍵、平均 **temp_diff** 做為值，而 **targettemp** 做為群組。
4. 重新啟動 Spark SQL 解譯器以結束應用程式。 按一下頂端的 [解譯器] 索引標籤，然後針對 Spark 解譯器按一下 [重新啟動]。

    ![重新啟動 Zeppelin 解譯器](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "重新啟動 Zeppelin 解譯器")

### <a name="run-hive-statements"></a>執行 hive 陳述式
1. 從 Zeppelin Notebook，按一下 [解譯器]  按鈕。

    ![更新 Hive 解譯器](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-1.png "更新 Hive 解譯器")
2. 針對 **hive** 解譯器，按一下 [編輯]。

    ![更新 Hive 解譯器](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-2.png "更新 Hive 解譯器")

    更新下列屬性。

   * 將 **default.password** 設為建立 HDInsight Spark 叢集時為系統管理使用者指定的密碼。
   * 將 **default.url** 設為 `jdbc:hive2://<spark_cluster_name>.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2`。 以您 Spark 叢集的名稱取代 **\<spark_cluster_name>**。
   * 將 **default.user** 設為建立叢集時指定的系統管理使用者名稱。 例如， *admin*。
3. 按一下 [儲存]，當系統提示您重新啟動 hive 解譯器時，按一下 [確定]。
4. 建立新的 Notebook 並執行以下陳述式，以列出叢集上的所有 hive 資料表。

        %hive
        SHOW TABLES

    根據預設，HDInsight 叢集有稱為 **hivesampletable** 的範例資料表，所以您應該會看到以下輸出。

    ![Hive 輸出](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-3.png "Hive 輸出")
5. 執行下列陳述式，以列出資料表中的記錄。

        %hive
        SELECT * FROM hivesampletable LIMIT 5

    您應該會看到如下所示的輸出。

    ![Hive 輸出](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-4.png "Hive 輸出")

## <a name="seealso"></a>另請參閱
* [概觀：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>案例
* [Spark 和 BI：在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)
* [Spark 和機器學習服務：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和機器學習服務：使用 HDInsight 中的 Spark 來預測食品檢查結果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark 串流：使用 HDInsight 中的 Spark 來建置即時串流應用程式](hdinsight-apache-spark-eventhub-streaming.md)
* [使用 HDInsight 中的 Spark 進行網站記錄分析](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>建立及執行應用程式
* [使用 Scala 建立獨立應用程式](hdinsight-apache-spark-create-standalone-application.md)
* [利用 Livy 在 Spark 叢集上遠端執行作業](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和擴充功能
* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (使用 IntelliJ IDEA 的 HDInsight Tools 外掛程式來建立和提交 Spark Scala 應用程式)](hdinsight-apache-spark-intellij-tool-plugin.md)
* [使用 IntelliJ IDEA 的 HDInsight Tools 外掛程式遠端偵錯 Spark 應用程式](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight 的 Spark 叢集中 Jupyter Notebook 可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [搭配 Jupyter Notebook 使用外部套件](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理資源
* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

