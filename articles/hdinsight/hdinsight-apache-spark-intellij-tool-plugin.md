---
title: "使用 Azure Toolkit for IntelliJ 建立適用於 Spark 的 Scala 應用程式 | Microsoft Docs"
description: "使用 HDInsight 工具 (位於 IntelliJ 的 Azure 工具組中) 來開發以 Scala 撰寫的 Spark 應用程式，並將它們提交到 HDInsight Spark 叢集。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 73304272-6c8b-482e-af7c-cd25d95dab4d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 0d94ac5f667bd31abe82781cfed3f9adbd27b364
ms.contentlocale: zh-tw
ms.lasthandoff: 04/27/2017


---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-hdinsight-cluster"></a>使用 IntelliJ 的 Azure 工具組建立適用於 HDInsight 叢集的 Spark 應用程式

使用 HDInsight 工具 (位於 IntelliJ 的 Azure 工具組中) 來開發以 Scala 撰寫的 Spark 應用程式，並直接從 IntelliJ IDE 將它們提交到 HDInsight Spark 叢集。 您能以數種不同的方式使用 HDInsight 工具外掛程式：

* 在 HDInsight Spark 叢集上開發並提交 Scala Spark 應用程式
* 存取您的 Azure HDInsight Spark 叢集資源
* 在本機開發並執行 Scala Spark 應用程式

您也可以遵循 [這裡](https://mix.office.com/watch/1nqkqjt5xonza) 的影片來協助您開始。

> [!IMPORTANT]
> 此工具可用來只對 Linux 上的 HDInsight Spark 叢集建立並提交應用程式。
> 
> 

## <a name="prerequisites"></a>必要條件
* Azure 訂用帳戶。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* HDInsight Linux 上的 Apache Spark 叢集。 如需指示，請參閱 [在 Azure HDInsight 中建立 Apache Spark 叢集](hdinsight-apache-spark-jupyter-spark-sql.md)。
* Oracle Java Development Kit。 您可以從 [這裡](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)加以安裝。
* IntelliJ IDEA。 本文章使用 15.0.1 版。 您可以從 [這裡](https://www.jetbrains.com/idea/download/)加以安裝。

## <a name="install-hdinsight-tools-in-azure-toolkit-for-intellij"></a>安裝適用於 IntelliJ 的 Azure 工具組中的 HDInsight 工具
適用於 IntelliJ 的 HDInsight 工具是適用於 IntelliJ 的 Azure 工具組的一部分。 如需有關如何安裝 Azure 工具組的指示，請參閱 [安裝 Azure Toolkit for IntelliJ](../azure-toolkit-for-intellij-installation.md)。

## <a name="log-into-your-azure-subscription"></a>登入您的 Azure 訂用帳戶
1. 啟動 IntelliJ IDE，然後開啟 [Azure Explorer]。 從 IDE 的 [檢視] 功能表中，按一下 [工具視窗]，然後按一下 [Azure Explorer]。
   
    ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/show-azure-explorer.png)
2. 以滑鼠右鍵按一下 [Azure Explorer] 中的 [Azure] 節點，然後按一下 [登入]。
3. 在 [Azure 登入] 對話方塊中，按一下 [登入] 並輸入您的 Azure 認證。
   
    ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-2.png)
4. 登入之後，[選取訂用帳戶] 對話方塊會列出與認證相關聯的所有 Azure 訂用帳戶。 按一下對話方塊中的 [選取] 以關閉對話方塊。

    ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/Select-Subscriptions.png)
5. 在 [Azure Explorer] 索引標籤中展開 [HDInsight]，以查看您訂用帳戶下的 HDInsight Spark 叢集。
   
    ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-3.png)
6. 您可以進一步展開叢集名稱節點，查看與叢集相關聯的資源 (例如儲存體帳戶)。
   
    ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>在 HDInsight Spark 叢集上執行 Spark Scala 應用程式
1. 啟動 IntelliJ IDEA 並建立專案。 在新增專案對話方塊中選取下列選項，然後按 [下一步] 。
   
    ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)
   
   * 從左窗格中，選取 [HDInsight] 。
   * 從右窗格中，選取 [HDInsight 上的 Spark (Scala)] 。
   * 按 [下一步] 。
2. 在下一個視窗中，提供專案詳細資料。
   
   * 提供專案名稱和專案位置。
   * 對於 **Project SDK**，使用適用於 Spark 1.6 和 Spark 2.0 叢集的 Java 1.8。
   * 對於 **Scala SDK**，請依序按一下 [建立]、[下載]，然後選取要使用的 Scala 版本。
   * * 如果您意願將作業提交至 Spark 2.0 叢集，請選擇 **JDK 1.8 和 Scala 2.11.x**。
   * * 如果您要將作業提交至 Spark 1.6 叢集，請選擇 **JDK 1.8 (語言層級 7) 和 Scala 2.10.x**。

        ![](./media/hdinsight-apache-spark-intellij-tool-plugin/show-scala2.11.x-select.png)
   * 對於 **Spark SDK**，請從[這裡] (http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409) 下載和使用 SDK (spark-assembly-2.0.0-hadoop2.7.0-SNAPSHOT.jar 適用於 Spark 2.0 叢集，而 spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar 適用於 Spark 1.6 叢集)。 您也可以改用 [Spark Maven 存放庫](http://mvnrepository.com/search?q=spark)，不過請確定您已安裝正確的 Maven 存放庫，以便開發 Spark 應用程式。 (例如，假設您使用 Spark Streaming，請確定已安裝 Spark Streaming 組件。 另外請確定您針對 Spark 1.6 叢集使用標示為 Scala 2.10 的存放庫，以及針對 Spark 2.0 叢集使用標示為 Scala 2.11 的存放庫。
     
       ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)
   * 按一下 [完成] 。
3. Spark 專案會自動為您建立構件。 若要查看構件，請遵循下列步驟。
   
   1. 在 [檔案] 功能表中，按一下 [專案結構]。
   2. 在 [專案結構] 對話方塊中，按一下 [構件]，以查看建立的預設構件。
      
       ![建立 JAR](./media/hdinsight-apache-spark-intellij-tool-plugin/default-artifact.png)
      
      您也可以建立自己的構件，方法是按一下上圖中強調顯示的 **+** 圖示。
4. 在 [專案結構] 對話方塊中，按一下 [專案]。 如果 [專案 SDK] 設定為 1.8，請確定 [專案語言層級] 設為 [7 - Diamonds、ARM、Multi-Catch 等] \(對 Spark 2.0 叢集而言為選擇性)。
   
    ![設定專案語言層級](./media/hdinsight-apache-spark-intellij-tool-plugin/set-project-language-level.png)
5. 新增應用程式的原始程式碼。
   
   1. 從 [專案總管] 中，以滑鼠右鍵按一下 [src]、指向 [新增]，然後按一下 [Scala 類別]。
      
       ![新增原始程式碼](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)
   2. 在 [建立新的 Scala 類別] 對話方塊中，提供一個名稱，並針對 [種類] 選取 [物件]，然後按一下 [確定]。
      
       ![新增原始程式碼](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)
   3. 在 **MyClusterApp.scala** 檔案中，貼入下列程式碼。 此程式碼會從 HVAC.csv (所有 HDInsight Spark 叢集上均有提供) 讀取資料、擷取在 CSV 的第七個資料行中只有個位數的資料列，並將輸出寫入到叢集預設儲存體容器下的 **/HVACOut** 。

            import org.apache.spark.SparkConf
            import org.apache.spark.SparkContext

            object MyClusterApp{
              def main (arg: Array[String]): Unit = {
                val conf = new SparkConf().setAppName("MyClusterApp")
                val sc = new SparkContext(conf)

                val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

                //find the rows which have only one digit in the 7th column in the CSV
                val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

                rdd1.saveAsTextFile("wasbs:///HVACOut")
              }

            }

1. 在 HDInsight Spark 叢集上執行應用程式。
   
   1. 從 [專案總管] 中，以滑鼠右鍵按一下專案名稱，然後選取 [將 Spark 應用程式提交給 HDInsight]。
      
       ![提交 Spark 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)
   2. 系統會提示您輸入 Azure 訂用帳戶認證。 在 [提交 Spark] 對話方塊中，提供下列值：
      
      * 針對 [Spark clusters (Linux only) (Spark 叢集 (僅限 Linux))] ，選取您要在其上執行應用程式的 HDInsight Spark 叢集。
      * 您需要從 IntelliJ 專案中選取構件，或從硬碟中選取一個。
      * 針對 [主要類別名稱] 文字方塊，按一下省略符號 (![ellipsis](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png))，並在應用程式的原始程式碼中選取主要類別，然後按一下 [確定]。
        
          ![提交 Spark 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)
      * 因為此範例中的應用程式程式碼不需要任何命令列引數，或是參考 JAR 或檔案，所以您可以將其餘的文字方塊保留空白。
      * 提供所有輸入之後，對話方塊應該如下圖所示。
        
          ![提交 Spark 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)
      * 按一下 [提交] 。
   3. 視窗底部的 [Spark Submission (提交 Spark)]  索引標籤應會開始顯示進度。 您也可以按一下 [Spark Submission] \(提交 Spark) 視窗中的紅色按鈕，即可停止應用程式。
      
       ![Spark 應用程式結果](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      在＜存取和管理 HDInsight Spark 叢集...＞一節中，您會了解如何使用適用於 IntelliJ 之 Azure 工具組中的 HDInsight 工具來存取作業輸出。

## <a name="choose-adls-as-a-spark-scala-application-storage"></a>選擇 ADLS 做為 Spark Scala 應用程式儲存體
* 如果您想要將應用程式提交至 ADLS，則在 Azure 登入程序期間必須選擇 [互動式] 模式。 

    ![互動式登入](./media/hdinsight-apache-spark-intellij-tool-plugin/authentication-interactive.png)

* 如果您以 [自動] 模式提交，會發生下列錯誤：

    ![登入錯誤](./media/hdinsight-apache-spark-intellij-tool-plugin/authentication-error.png)

## <a name="access-and-manage-hdinsight-spark-clusters-using-the-hdinsight-tools-in-azure-toolkit-for-intellij"></a>使用適用於 IntelliJ 的 Azure 工具組中的 HDInsight 工具存取和管理 HDInsight Spark 叢集
您可以使用 HDInsight 工具 (此工具包含在適用於 IntelliJ 的 Azure 工具組中) 來執行各種作業。

### <a name="access-the-job-view-directly-from-the-hdinsight-tools"></a>直接從 HDInsight 工具存取作業檢視
1. 從 [Azure Explorer] 中，依序展開 [HDInsight] 和 Spark 叢集名稱，然後按一下 [作業]。
2. 在右窗格中，[Spark 作業檢視]  索引標籤會顯示已在叢集上執行的所有應用程式。 按一下您想要查看更多詳細資料的應用程式名稱。
   
    ![存取作業檢視](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)
3. 適用於 [錯誤訊息]、[作業輸出]、[Livy 作業記錄] 及 [Spark 驅動程式記錄] 的方塊都會根據您選取的應用程式來填入。
4. 您也可以按一下畫面頂端的個別按鈕來開啟 [Spark 歷程記錄 UI] 和 [YARN UI] \(應用程式層級)。

### <a name="access-the-spark-history-server"></a>存取 Spark 歷程記錄伺服器
1. 從 [Azure Explorer] 中，展開 [HDInsight]、以滑鼠右鍵按一下您的 Spark 叢集名稱，然後選取 [開啟 Spark 歷程記錄 UI]。 出現提示時，輸入叢集的系統管理員認證。 在佈建叢集時，您必須已指定這些項目。
2. 在 [Spark 歷程記錄伺服器] 儀表板中，您可以使用應用程式名稱，尋找您剛完成執行的應用程式。 在上述程式碼中，您使用 `val conf = new SparkConf().setAppName("MyClusterApp")`來設定應用程式名稱。 因此，Spark 應用程式名稱為 **MyClusterApp**。

### <a name="launch-the-ambari-portal"></a>啟動 Ambari 入口網站
從 [Azure Explorer] 中，展開 [HDInsight]、以滑鼠右鍵按一下您的 Spark 叢集名稱，然後選取 [開啟叢集管理入口網站 (Ambari)]。 出現提示時，輸入叢集的系統管理員認證 (在叢集佈建程序期間所指定)。

### <a name="manage-azure-subscriptions"></a>管理 Azure 訂用帳戶
根據預設，HDInsight 工具會列出您所有 Azure 訂用帳戶中的 Spark 叢集。 如有需要，您可以指定要存取其叢集的訂用帳戶。 從 [Azure Explorer] 中，以滑鼠右鍵按一下 [Azure] 根節點，然後按一下 [管理訂用帳戶]。 從對話方塊中，清除您不想存取的訂用帳戶核取方塊，然後按一下 [關閉] 。 如果您想要從 Azure 訂用帳戶登出，也可以按一下 [登出]  。

## <a name="run-a-spark-scala-application-locally"></a>在本機執行 Spark Scala 應用程式
您可以使用適用於 IntelliJ 的 Azure 工具組中的 HDInsight 工具，在本機工作站上執行 Spark Scala 應用程式。 一般而言，這類應用程式不需要存取叢集資源 (例如儲存體容器)，而且可在本機上執行並測試。

### <a name="prerequisite"></a>必要條件
在 Windows 電腦上執行本機 Spark Scala 應用程式時，可能會發生 [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) 中所述的例外狀況，此例外狀況發生的原因是因為 Windows 上缺少 WinUtils.exe。 若要解決這個錯誤，您必須[從這裡將可執行檔下載](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)到如 **C:\WinUtils\bin** 的位置，然後新增環境變數 **HADOOP_HOME**，並將變數的值設為 **C\WinUtils**。

### <a name="run-a-local-spark-scala-application"></a>執行本機 Spark Scala 應用程式
1. 啟動 IntelliJ IDEA 並建立專案。 在新增專案對話方塊中選取下列選項，然後按 [下一步] 。
   
    ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)
   
   * 從左窗格中，選取 [HDInsight] 。
   * 從右窗格中，選取 [Spark on HDInsight Local Run Sample (Scala) (HDInsight 上的 Spark 本機執行範例 (Scala))] 。
   * 按 [下一步] 。
2. 在下一個視窗中，提供專案詳細資料。
   
   * 提供專案名稱和專案位置。
   * 對於 [專案 SDK] ，請確定您提供的 Java 版本大於 7。
   * 對於 [Scala SDK]，請依序按一下 [建立]、[下載]，然後選取要使用的 Scala 版本。**Scala 2.11.x 適用於 Spark 2.0，Scala 2.10.x 適用於 Spark 1.6**
     
       ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)
   * 對於 **Spark SDK**，請從 [這裡](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409)下載並使用 SDK。 您也可以略過此項，並改用 [Spark Maven Repository](http://mvnrepository.com/search?q=spark) ，不過請確定您已安裝正確的 Maven 儲存機制，以便開發 Spark 應用程式。 (例如，如果您使用 Spark Streaming，必須確定您已安裝 Spark Streaming 組件；也請確定您使用的儲存機制，標示為 Scala 2.10 適用於 Spark 1.6 叢集，標示為 Scala 2.11 適用於 Spark 2.0 叢集)。
     
       ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-create-project.png)
   * 按一下 [完成] 。
3. 範本會在 **src** 資料夾下方新增可在電腦本機執行的程式碼範例 (**LogQuery**)。
   
    ![本機 Scala 應用程式](./media/hdinsight-apache-spark-intellij-tool-plugin/local-app.png)
4. 以滑鼠右鍵按一下 [LogQuery] 應用程式，然後按一下 [執行 'LogQuery']。 您會在底部的 [執行] 索引標籤上看到如下的輸出。
   
   ![Spark 應用程式本機執行結果](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="convert-existing-intellij-idea-applications-to-use-the-hdinsight-tools-in-azure-toolkit-for-intellij"></a>轉換現有的 IntelliJ IDEA 應用程式以使用適用於 IntelliJ 的 Azure 工具組中的 HDInsight 工具
您也可以轉換 IntelliJ IDEA 中建立的 Spark Scala 應用程式，以便與適用於 IntelliJ 的 Azure 工具組中的 HDInsight 工具相容。 此功能可讓您使用工具，將應用程式提交到 HDInsight Spark 叢集。 您可以執行下列步驟來這麼做：

1. 對於使用 IntelliJ IDEA 建立的現有 Spark Scala 應用程式，請開啟相關聯的.iml 檔案。
2. 在根層級中，您會看到如下的 **module** 元素：
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
3. 編輯該元素以加入 `UniqueKey="HDInsightTool"` ，使 **module** 元素看起來如下所示：
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
4. 儲存變更。 您的應用程式現在應該可與適用於 IntelliJ 的 Azure 工具組中的 HDInsight 工具相容。 您可以滑鼠右鍵按一下 [專案總管] 中的專案名稱來測試。 快顯功能表現在有 [將 Spark 應用程式提交給 HDInsight] 的選項。

## <a name="troubleshooting"></a>疑難排解
### <a name="please-use-a-larger-heap-size-error-in-local-run"></a>本機執行發生「請使用較大的堆積大小」錯誤
在 Spark 1.6 中，如果您在本機執行期間使用 32 位元的 Java SDK，您可能會遇到下列錯誤︰

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

這是因為堆積大小不足，導致 Spark 無法執行，因為 Spark 至少需要 471 MB (如有需要，您可以從 [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081) 取得更多詳細資料)。 其中一個簡單的解決方案就是使用 64 位元的 Java SDK。 您也可以新增下列選項來變更 IntelliJ 中的 JVM 設定：

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Spark 應用程式本機執行結果](./media/hdinsight-apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="feedback--known-issues"></a>意見反應和已知問題
目前不支援直接檢視 Spark 輸出，我們正在處理此功能。

如果您有任何建議或意見反應，或使用此工具時，遇到任何問題，歡迎寄電子郵件到 hdivstool@microsoft.com。

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
* [使用適用於 IntelliJ 的 Azure 工具組中的 HDInsight 工具遠端偵錯 Spark 應用程式](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [使用適用於 Eclipse 的 Azure 工具組中的 HDInsight 工具建立 Spark 應用程式](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [利用 HDInsight 上的 Spark 叢集來使用 Zeppelin Notebook](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [HDInsight 的 Spark 叢集中 Jupyter Notebook 可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [搭配 Jupyter Notebook 使用外部套件](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理資源
* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](hdinsight-apache-spark-job-debugging.md)


