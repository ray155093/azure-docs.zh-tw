---
title: "使用 Azure Toolkit for Eclipse 建立適用於 Spark 的 Scala 應用程式 | Microsoft Docs"
description: "了解如何建立在 HDInsight Spark 叢集上執行的獨立 Spark 應用程式。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f6c79550-5803-4e13-b541-e86c4abb420b
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: b304f66f71142606391ecbb3f29b917c32ba876a
ms.lasthandoff: 01/24/2017


---
# <a name="use-hdinsight-tools-in-azure-toolkit-for-eclipse-to-create-spark-applications-for-hdinsight-spark-cluster"></a>使用 Azure Toolkit for Eclipse 中的 HDInsight 工具建立 HDInsight Spark 叢集的 Spark 應用程式
本文將逐步指引您開發以 Scala 撰寫的 Spark 應用程式，以及使用適用於 Eclipse 的 Azure 工具組中的 HDInsight 工具，將它提交給 HDInsight Spark 叢集。 您可以利用數個不同的方式來使用此工具：

* 在 HDInsight Spark 叢集上開發並提交 Scala Spark 應用程式
* 存取您的 Azure HDInsight Spark 叢集資源
* 在本機開發並執行 Scala Spark 應用程式

> [!IMPORTANT]
> 此工具可用來只對 Linux 上的 HDInsight Spark 叢集建立並提交應用程式。
> 
> 

## <a name="prerequisites"></a>必要條件
* Azure 訂用帳戶。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱 [在 Azure HDInsight 中建立 Apache Spark 叢集](hdinsight-apache-spark-jupyter-spark-sql.md)。
* Oracle Java Development kit 第 7 版和第 8 版。 
  
  * **Java SDK 7** 來編譯 Spark 專案。 您可以從 [這裡](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)下載 Java SDK 7。
  * **Java SDK 8** 會用於 Eclipse 整合式開發環境 (IDE) 執行階段。 您可以從 [這裡](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下載。
* Eclipse IDE。 本文使用的是 Eclipse Neon。 您可以從 [這裡](https://www.eclipse.org/downloads/)加以安裝。
* Scala IDE for Eclipse。 
  
  * **如果您已安裝 Eclipse IDE**，您可以前往 [說明] -> [安裝新軟體] 新增 Scala IDE 外掛程式，然後將 [http://download.scala-ide.org/sdk/lithium/e44/scala211/stable/site](http://download.scala-ide.org/sdk/lithium/e44/scala211/stable/site) 新增為來源以下載適用於 Eclipse 的 Scala 外掛程式。 
  * **如果您沒有安裝 Eclipse IDE**，可以直接從 [這裡](http://scala-ide.org/download/sdk.html)安裝 Scala IDE。 您可以從此連結下載 .zip 檔案，將其解壓縮後，瀏覽至 **/eclipse** 資料夾，然後從該資料夾執行 **eclipse.exe** 檔案。
    
    > [!NOTE]
    > 這份文件中的步驟是使用已安裝 Scala 外掛程式的 Eclipse IDE 為依據。
    > 
    > 
* Spark SDK。 您可以從 [這裡](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409)下載。
* 從 [https://www.eclipse.org/efxclipse/install.html](https://www.eclipse.org/efxclipse/install.html)安裝 e(fx)clipse。

## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>安裝適用於 Eclipse 的 Azure 工具組中的 HDInsight 工具
適用於 Eclipse 的 HDInsight 工具是適用於 Eclipse 的 Azure 工具組的一部分。 如需如何安裝 Azure 工具組的指示，請參閱 [安裝 Azure Toolkit for Eclipse](../azure-toolkit-for-eclipse-installation.md)。

## <a name="log-into-your-azure-subscription"></a>登入您的 Azure 訂用帳戶
1. 啟動 Eclipse IDE，然後開啟 [Azure Explorer]。 在整合式開發環境 (IDE) 中的 [視窗] 功能表上，按一下[顯示檢視]，然後按一下 [其他]。 從開啟的對話方塊中展開 [Azure]，然後依序按一下 [Azure Explorer] 和 [確定]。
   
    ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-1.png)
2. 以滑鼠右鍵按一下 [Azure Explorer] 中的 [Azure] 節點，然後按一下 [管理訂用帳戶]。
3. 在 [管理訂用帳戶] 對話方塊中，按一下 [登入] 並輸入您的 Azure 認證。
   
    ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-2.png)
4. 登入之後，[管理訂用帳戶]  對話方塊會列出與認證相關聯的所有 Azure 訂用帳戶。 按一下對話方塊中的 [關閉]  。
5. 在 [Azure Explorer] 索引標籤中展開 [HDInsight]  ，以查看您訂用帳戶下的 HDInsight Spark 叢集。
   
    ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-3.png)
6. 您可以進一步展開叢集名稱節點，查看與叢集相關聯的資源 (例如儲存體帳戶)。
   
    ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>設定 HDInsight Spark 叢集的 Spark Scala 專案
1. 在 Eclipse IDE 工作區中，依序按一下 [檔案]、[新增] 及 [專案]。 
2. 在 [新增專案] 精靈中展開 [HDInsight]、選取 [HDInsight 上的 Spark (Scala)]，然後按 [下一步]。
   
    ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
3. 在 [新增 HDInsight Scala 專案] 對話方塊中，輸入/選取值 (如下圖所示)，然後按 [下一步]。
   
    ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
   
   * 輸入專案的名稱。
   * 在 [JRE] 方塊中，請確定 [使用執行環境 JRE] 已設為 [JavaSE-1.7]。
   * 請確定 Spark SDK 已設為您下載 SDK 的位置。 下載位置的連結已包含於本主題稍早的 [必要條件](#prerequisites) 中。 您也可以從包含在此對話方塊中的連結下載 SDK，如上圖所示。     
4. 在下一個對話方塊中，按一下 [程式庫] 索引標籤，然後按兩下 [JRE System Library [JavaSE-1.7]]。
   
    ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-4.png)
5. 在 [編輯程式庫] 對話方塊中，確定 [執行環境] 已設為 [JavaSE-1.7(jdk1.7.0_79)]。 如果這不是可用選項，請遵循下列步驟。
   
   1. 選取 [替代 JRE] 選項，然後檢查 [JavaSE-1.7(jdk1.7.0_79)] 是否可用。
   2. 如果不可用，請按一下 [安裝的 JRE]  按鈕。
      
         ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-5.png)
   3. 在 [安裝的 JRE] 對話方塊中，按一下 [新增]。
      
         ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-6.png)    
   4. 在 [JRE 類型] 對話方塊中，選取 [標準 VM]，然後按 [下一步]
      
         ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-7.png)    
   5. 在 [JRE Definition (JRE 定義)] 對話方塊中按一下 [目錄]，然後瀏覽至 JDK 7 安裝位置，並選取 [jdk1.7.0_79] 的根資料夾。
      
         ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-8.png)    
   6. 按一下 [完成] 。 在 [安裝的 JRE] 對話方塊中，選取新增的 JRE，然後按一下 [確定]。
      
          ![Create Spark Scala application](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-9.png)
   7. 系統應會針對 [執行環境] 列出新增的 JRE。 按一下 [完成] 。
      
            ![Create Spark Scala application](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-10.png)
6. 返回 [程式庫] 索引標籤，按兩下 [Scala Library Container[2.11.8]]。 在 [編輯程式庫] 對話方塊中，選取 [Fixed Scala Library container:2.10.6]。 
   
    ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-11.png)
   
    按一下 [完成]  ，直到您離開專案設定對話方塊為止。

## <a name="create-a-scala-application-for-hdinsight-spark-cluster"></a>建立 HDInsight Spark 叢集的 Scala 應用程式
1. 在已開啟的 Eclipse IDE 中，從 [封裝總管] 中展開您稍早建立的專案，以滑鼠右鍵按一下 [src]、指向 [新增]，然後按一下 [其他]。
2. 在 [選取精靈] 對話方塊方塊中，展開 [Scala 精靈]、按一下 [Scala 物件]，然後按 [下一步]。
   
    ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
3. 在 [建立新檔案] 對話方塊中輸入物件的名稱，然後按一下 [完成]。
   
    ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
4. 在文字編輯器中貼上下列程式碼。
   
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
5. 在 HDInsight Spark 叢集上執行應用程式。
   
   1. 從 [封裝總管] 中，以滑鼠右鍵按一下專案名稱，然後選取 [將 Spark 應用程式提交給 HDInsight]。        
   2. 在 [Spark Submission (提交 Spark)]  對話方塊中，提供下列值。
      
      * 針對 **叢集名稱**，選取您要在其上執行應用程式的 HDInsight Spark 叢集。
      * 您需要從 Eclipse 專案中選取構件，或從硬碟中選取一個。
      * 針對 **主要類別名稱** 文字方塊，輸入您在程式碼中指定的物件名稱 (請參閱下圖)。
        
          ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
      * 因為此範例中的應用程式程式碼不需要任何命令列引數或參考 JAR 或檔案，所以您可以將其餘的文字方塊保留空白。
      * 按一下 [提交] 。
   3. [Spark Submission (提交 Spark)]  索引標籤應會開始顯示進度。 您可以按一下 [Spark Submission (提交 Spark)] 視窗中的紅色按鈕，即可停止應用程式。 您也可以按一下全球圖示 (以映像中的藍色方塊表示)，檢視此特定應用程式執行的記錄檔。
      
       ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)
      
      在下節中，您會了解如何使用適用於 Eclipse 的 Azure 工具組中的 HDInsight 工具來存取作業輸出。

## <a name="access-and-manage-hdinsight-spark-clusters-using-the-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>使用適用於 Eclipse 的 Azure 工具組中的 HDInsight 工具存取和管理 HDInsight Spark 叢集
您可以使用 HDInsight 工具來執行各種作業。

### <a name="access-the-storage-container-for-the-cluster"></a>存取叢集的儲存體容器
1. 從 [Azure Explorer] 中展開 [HDInsight]  根節點，以查看可用的 HDInsight Spark 叢集清單。
2. 展開叢集名稱以查看叢集的儲存體帳戶和預設儲存體容器。
   
    ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-5.png)
3. 按一下與叢集相關聯的儲存體容器名稱。 在右窗格中，您應該會看到名稱為 **HVACOut**的資料夾。 按兩下以開啟資料夾，即可看到 **part-*** 檔案。 開啟其中一個檔案以查看應用程式的輸出。

### <a name="access-the-spark-history-server"></a>存取 Spark 歷程記錄伺服器
1. 從 [Azure 總管]中，以滑鼠右鍵按一下您的 Spark 叢集名稱，然後選取 [開啟 Spark 歷程記錄 UI]。 出現提示時，輸入叢集的系統管理員認證。 在佈建叢集時，您必須已指定這些項目。
2. 在 [Spark 歷程記錄伺服器] 儀表板中，您可以使用應用程式名稱，尋找您剛完成執行的應用程式。 在上述程式碼中，您使用 `val conf = new SparkConf().setAppName("MyClusterApp")`來設定應用程式名稱。 因此，Spark 應用程式名稱為 **MyClusterApp**。

### <a name="launch-the-ambari-portal"></a>啟動 Ambari 入口網站
從 [Azure Explorer] 中，以滑鼠右鍵按一下您的 Spark 叢集名稱，然後選取 [開啟叢集管理入口網站 (Ambari)]。 出現提示時，輸入叢集的系統管理員認證。 在佈建叢集時，您必須已指定這些項目。

### <a name="manage-azure-subscriptions"></a>管理 Azure 訂用帳戶
根據預設，適用於 Eclipse 的 Azure 工具組中的 HDInsight 工具會列出您所有 Azure 訂用帳戶中的 Spark 叢集。 如有需要，您可以指定要存取其叢集的訂用帳戶。 從 [Azure Explorer] 中，以滑鼠右鍵按一下 [Azure] 根節點，然後按一下 [管理訂用帳戶]。 從對話方塊中，清除您不想存取的訂用帳戶核取方塊，然後按一下 [關閉] 。 如果您想要從 Azure 訂用帳戶登出，也可以按一下 [登出]  。

## <a name="run-a-spark-scala-application-locally"></a>在本機執行 Spark Scala 應用程式
您可以使用適用於 Eclipse 的 Azure 工具組中的 HDInsight 工具，在本機工作站上執行 Spark Scala 應用程式。 一般而言，這類應用程式不需要存取叢集資源 (例如儲存體容器)，而且可在本機上執行並測試。

### <a name="prerequisite"></a>必要條件
在 Windows 電腦上執行本機 Spark Scala 應用程式時，可能會發生 [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) 中所述的例外狀況，此例外狀況發生的原因是因為 Windows 作業系統上缺少 **WinUtils.exe** 。 若要解決這個錯誤，您必須[從這裡下載可執行檔](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)，並將其放至 **C:\WinUtils\bin** 之類的位置。 然後，您必須新增環境變數 **HADOOP_HOME**，並將變數的值設為 **C\WinUtils**。

### <a name="run-a-local-spark-scala-application"></a>執行本機 Spark Scala 應用程式
1. 啟動 Eclipse，然後建立新的專案。 在新增專案對話方塊中選取下列選項，然後按 [下一步] 。
   
    ![建立 Spark Scala 應用程式](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
   
   * 從左窗格中，選取 [HDInsight] 。
   * 從右窗格中，選取 [Spark on HDInsight Local Run Sample (Scala) (HDInsight 上的 Spark 本機執行範例 (Scala))] 。
   * 按 [下一步] 。
2. 若要提供專案的詳細資訊，請遵循前面的[設定 HDInsight Spark 叢集的 Spark Scala 應用程式專案](#set-up-a-spark-scala-application-project-for-an-hdinsight-spark cluster)一節中的步驟 3 到 6。
3. 範本會在 **src** 資料夾下方新增可在電腦本機執行的程式碼範例 (**LogQuery**)。
   
    ![Spark 應用程式本機執行結果](./media/hdinsight-apache-spark-eclipse-tool-plugin/local-app.png)
4. 以滑鼠右鍵按一下 **LogQuery** 應用程式、指向 [執行身分]，然後按一下 [1 Scala 應用程式]。 您將在底部的 [Console (主控台)]  索引標籤上看到如下的輸出。
   
   ![Spark 應用程式本機執行結果](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="feedback--known-issues"></a>意見反應和已知問題
目前不支援直接檢視 Spark 輸出，我們正在處理此問題。

如果您有任何建議或意見反應，或使用此工具時遇到任何問題，歡迎寄電子郵件到 hdivstool@microsoft.com。

## <a name="a-nameseealsoasee-also"></a><a name="seealso"></a>另請參閱
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
* [使用適用於 IntelliJ 的 Azure 工具組中的 HDInsight 工具建立和提交 Spark Scala 應用程式](hdinsight-apache-spark-intellij-tool-plugin.md)
* [使用適用於 IntelliJ 的 Azure 工具組中的 HDInsight 工具遠端偵錯 Spark 應用程式](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [利用 HDInsight 上的 Spark 叢集來使用 Zeppelin Notebook](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [HDInsight 的 Spark 叢集中 Jupyter Notebook 可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [搭配 Jupyter Notebook 使用外部套件](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理資源
* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](hdinsight-apache-spark-job-debugging.md)


