---
title: "適用於 IntelliJ 的 Azure 工具組 – 透過 SSH 對 Spark 應用程式進行遠端偵錯 | Microsoft Docs"
description: "如何使用適用於 IntelliJ 的 Azure 工具組中的 HDInsight 工具，透過 SSH 對 HDInsight 叢集上的應用程式進行遠端偵錯"
keywords: "遠端偵錯 intellij, 進行 intellij 遠端偵錯, ssh, intellij, hdinsight, 偵錯 intellij, 偵錯"
services: hdinsight
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 06/05/2017
ms.author: Jenny Jiang
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 5ee6504cd4bf69c8f2c14a3623ff537e66030ce1
ms.contentlocale: zh-tw
ms.lasthandoff: 07/17/2017

---
# <a name="remotely-debug-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>使用適用於 IntelliJ 的 Azure 工具組透過 SSH 對 HDInsight 叢集上的 Spark 應用程式進行遠端偵錯

本文提供的逐步指引，是關於如何使用適用於 IntelliJ 的 Azure 工具組中的 HDInsight 工具，對 HDInsight 叢集上的應用程式進行遠端偵錯。 您可以依照[影片](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)的作法，對您的專案進行偵錯。

**必要條件：**

* **適用於 IntelliJ 的 Azure 工具組中的 HDInsight 工具**。 這是適用於 IntelliJ 的 Azure 工具組的一部分。 如需詳細資訊，請參閱 [Installing the Azure Toolkit for IntelliJ (安裝適用於 IntelliJ 的 Azure 工具組)](https://docs.microsoft.com/en-us/azure/azure-toolkit-for-intellij-installation)。
* **適用於 IntelliJ 的 Azure 工具組**。 使用此工具組建立適用於 HDInsight 叢集的 Spark 應用程式。 如需詳細資訊，請依照[使用 IntelliJ 的 Azure 工具組建立適用於 HDInsight 叢集的 Spark 應用程式](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin)中的指示進行。
* **HDInsight SSH 服務與使用者名稱和密碼管理。** 如需詳細資訊，請參閱[使用 SSH 連線到 HDInsight (Hadoop)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) 和[使用 SSH 通道來存取 Ambari Web UI、JobHistory、NameNode、Oozie 及其他 Web UI](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel)。 
 

## <a name="create-a-spark-scala-application-and-configure-it-for-remote-debugging"></a>建立 Spark Scala 應用程式，並設定它以進行遠端偵錯
1. 啟動 IntelliJ IDEA 並建立專案。 在新增專案對話方塊中選取下列選項，然後按 [下一步] 。 本文以 [Spark on HDInsight Cluster Run Sample (Scala)] \(HDInsight 叢集上的 Spark 執行範例 (Scala)\) 為例。

     ![建立偵錯專案](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-create-projectfor-debug-remotely.png)
   - 在左窗格中，選取 [HDInsight]。
   - 在右窗格中，根據您的喜好設定選取 Java 或 Scala 範本。 選擇下列選項： 
      - **Spark on HDInsight (Scala) \(HDInsight 上的 Spark (Scala)\)**
      - **Spark on HDInsight (Java) \(HDInsight 上的 Spark (Java)\)**
      - Spark on HDInsight Cluster Run Sample (Scala) \(HDInsight 叢集上的 Spark 執行範例 (Scala)\)
   - 建置工具：Scala 專案建立精靈支援管理相依性和專為 Scala 專案建置的 Maven 或 SBT。 您可以根據需求選取其中一項。
2. 在下一個視窗中，提供專案詳細資料。

   ![選取 Spark SDK](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-new-project.png)
   - 提供專案名稱和專案位置。
   - 針對 [Project SDK] \(專案 SDK\)，**Spark 2.x** 叢集請使用 **Java 1.8**，**Spark 1.x** 叢集請使用 **Java 1.7**。
   - 針對 [Spark Version] \(Spark 版本\)，Scala 專案建立精靈會為 Spark SDK 和 Scala SDK 整合正確的版本。 如果 Spark 叢集版本低於 2.0，請選擇 **Spark 1.x**。 否則，請選取 **Spark 2.x**。 本文以 **Spark 2.0.2 (Scala 2.11.8)** 為例。
3. 選取 **src** > **main \(主要\)** > scala 以在專案中開啟您的程式碼。 本文以 **SparkCore_wasbloTest** 指令碼為例。
4. 若要存取 [Edit Configurations] \(編輯設定\) 功能表，請選取右上角的圖示。 從這個功能表中，您可以建立或編輯遠端偵錯的設定。

   ![編輯設定](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-edit-configurations.png) 
5. 在 [Run/Debug Configurations] \(執行/偵錯設定\) 視窗中，按一下加號 (**+**)。 然後選取 [Submit Spark Job] \(提交 Spark 作業\) 選項。

   ![編輯設定](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-add-new-Configuration.png)
6. 輸入 [Name] \(名稱\)、**[Spark cluster] \(Spark 叢集\)** 和 [Main class name] \(主要類別名稱\)。 然後選取 [Advanced configuration] \(進階設定\)。 

   ![[Run/Debug Configurations] \(執行/偵錯設定\)](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-debug-configurations.png)
7. 在 [Spark Submission Advanced Configuration] \(Spark 提交進階設定\) 對話方塊中，選取 [Enable Spark remote debug] \(啟用 Spark 遠端偵錯\)。 輸入 SSH 使用者名稱或密碼，或使用私密金鑰檔案。 若要儲存它，請選取 [Ok] \(確定\)。

   ![[Enable Spark remote debug] \(啟用 Spark 遠端偵錯\)](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-enable-spark-remote-debug.png)
8. 設定現在會使用您提供的名稱儲存。 若要檢視設定詳細資訊，請選取設定名稱。 若要進行變更，請選取 [Edit Configurations] \(編輯設定\)。 
9. 完成組態設定之後，您可以針對遠端叢集執行專案，或執行遠端偵錯。

## <a name="learn-how-to-perform-remote-debugging-and-remote-run"></a>了解如何執行遠端偵錯和遠端執行
### <a name="scenario-1-perform-remote-run"></a>案例 1：執行遠端執行
1. 若要從遠端執行專案，請選取 [Run] \(執行\) 圖示。

   ![按一下 [Run] \(執行\) 圖示](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-icon.png)

2. [HDInsight Spark Submission] \(HDInsight Spark 提交\) 視窗會顯示應用程式執行狀態。 您可以根據此處的資訊來監督 Scala 作業的進度。

   ![按一下 [Run] \(執行\) 圖示](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-result.png)

### <a name="scenario-2-perform-remote-debugging"></a>案例 2：執行遠端偵錯
1. 設定一個中斷點，然後選取 [Debug] \(偵錯\) 圖示。

    ![按一下 [Debug] \(偵錯\) 圖示](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-icon.png)
2. 當程式執行觸達中斷點時，您會在下方窗格中看到 [Debugger] \(偵錯工具\) 索引標籤。 您也會在 [Variable] \(變數\) 視窗中看到檢視參數和變數資訊。 按一下 [Step Over] \(不進入函式\) 圖示，以移至下一行程式碼。 然後，您可以進一步逐步執行程式碼。 選取 [Resume Program] \(繼續程式\) 圖示，以繼續執行程式碼。 您可以檢閱 [HDInsight Spark Submission] \(HDInsight Spark 提交\) 視窗中的執行狀態。 

   ![[Debug] \(偵錯\) 索引標籤](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>案例 3：執行遠端偵錯和錯誤修正
在本節中，我們示範如何使用 IntelliJ 偵錯功能進行簡單修正，以動態更新變數值。 在下列程式碼範例中，因為目標檔案已存在，會擲回例外狀況。
  
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext

        object SparkCore_WasbIOTest {
          def main(arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkCore_WasbIOTest")
            val sc = new SparkContext(conf)
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            // Find the rows that have only one digit in the sixth column.
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)

            try {
              var target = "wasb:///HVACout2_testdebug1";
              rdd1.saveAsTextFile(target);
            } catch {
              case ex: Exception => {
                throw ex;
              }
            }
          }
        }


#### <a name="to-perform-remote-debugging-and-bug-fixing"></a>執行遠端偵錯和錯誤修正
1. 設定兩個中斷點，然後按一下 [Debug] \(偵錯\) 圖示以啟動遠端偵錯程序。

2. 程式碼會在第一個中斷點停止，參數及變數資訊會顯示在 [Variable] \(變數\) 視窗。 

3. 按一下 [Resume Program] \(繼續程式\) 圖示以繼續。 程式碼會在第二個點停止。 正如預期，會擲回例外狀況。

  ![擲回錯誤](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-throw-error.png) 

4. 再按一下 [Resume Program] \(繼續程式\) 圖示。 [HDInsight Spark Submission] \(HDInsight Spark 提交\) 視窗會顯示作業執行失敗錯誤。

  ![提交錯誤](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-error-submission.png) 

5. 若要使用 IntelliJ 偵錯功能動態更新變數值，請再次選取 [Debug] \(偵錯\)。 變數視窗會再次顯示。 

6. 以滑鼠右鍵按一下 [Debug] \(偵錯\) 索引標籤上的目標，然後選取 [Set Value] \(設定值\)。 接下來，輸入變數的新值。 然後選取 **Enter** 儲存值。 

  ![設定值](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-set-value.png) 

7. 按一下 [Resume Program] \(繼續程式\) 圖示，以繼續執行程式。 這次不會擲回例外狀況。 您可以看到專案成功執行而未發生任何例外狀況。

  ![偵錯而未發生例外狀況](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>另請參閱
* [概觀：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="demo"></a>示範
* 建立 Scala 專案 (影片)：[建立 Spark Scala 應用程式](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) \(Create Spark Scala Applications\)
* 遠端偵錯 (影片)：[使用適用於 IntelliJ 的 Azure 工具組對 HDInsight 叢集上的 Spark 應用程式進行遠端偵錯](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

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
* [使用適用於 IntelliJ 的 Azure 工具組，透過 VPN 在 HDInsight Spark 上對應用程式進行遠端偵錯](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [透過 Hortonworks 沙箱使用 HDInsight Tools for IntelliJ](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [使用適用於 Eclipse 的 Azure 工具組中的 HDInsight 工具建立 Spark 應用程式](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [利用 HDInsight 上的 Spark 叢集來使用 Zeppelin Notebook](hdinsight-apache-spark-zeppelin-notebook.md)
* [HDInsight 的 Spark 叢集中 Jupyter Notebook 可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [搭配 Jupyter Notebook 使用外部套件](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理資源
* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](hdinsight-apache-spark-job-debugging.md)
 
