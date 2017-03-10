---
title: "透過 Hortonworks 沙箱使用 Azure Toolkit for IntelliJ | Microsoft Docs"
description: "了解如何透過 Hortonworks 沙箱使用 Azure Toolkit for IntelliJ 中的 HDInsight 工具。"
keywords: "hadoop 工具,hive 查詢,intellij,hortonworks 沙箱,azure toolkit for intellij"
services: HDInsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: b587cc9b-a41a-49ac-998f-b54d6c0bdfe0
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/07/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: bb700c7de96712666bc4be1f8e430a2e94761f69
ms.openlocfilehash: b52e5bcf443afd05bf8cc37ce436cb80ab41412f
ms.lasthandoff: 01/24/2017


---
#<a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>透過 Hortonworks 沙箱使用 HDInsight Tools for IntelliJ

瞭解如何使用 HDInsight Tools for IntelliJ 開發 Apache Scala 應用程式和在工作站上執行的 [Hortonworks 沙箱](http://hortonworks.com/products/sandbox/)上測試應用程式。 [IntelliJ IDEA](https://www.jetbrains.com/idea/)是 Java 整合式開發環境 (IDE)，可用來開發電腦軟體。 在 Hortonworks 沙箱上開發並測試應用程式之後，您可以將它們移至 [Azure HDInsight](hdinsight-hadoop-introduction.md)。

##<a name="prerequisites"></a>必要條件

開始進行本教學課程之前，您必須具備：

- 在您的本機環境執行的 Hortonworks 沙箱上要有 HDP 2.4。 若要進行設定，請參閱[透過虛擬機器的 Hadoop 沙箱開始使用 Hadoop 生態系統](hdinsight-hadoop-emulator-get-started.md)。 請注意 HDInsight Tools for IntelliJ 只經過 HDP 2.4 測試。 從 [Hortonworks 沙箱下載網站](http://hortonworks.com/downloads/#sandbox)依序展開 [Hortonworks 沙箱封存] 來取得。
- [Java Developer Kit (JDK) 1.8 版或更新版本](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。 Azure Toolkit for IntelliJ 需要 JDK。
- [IntelliJ IDEA community 版本](https://www.jetbrains.com/idea/download)搭配使用 [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) 外掛程式與 [Azure Toolkit for IntelliJ](../azure-toolkit-for-intellij.md) 外掛程式。 HDInsight Tools for IntelliJ 會隨 Azure Toolkit for IntelliJ 一起提供。 

  **若要安裝外掛程式：**

  1. 開啟 IntelliJ IDEA。
  2. 從 [歡迎使用] 畫面中按一下 [設定]，然後按一下 [外掛程式]。
  3. 按一下左下角的 [安裝 JetBrains 外掛程式] 。
  4. 使用搜尋功能來搜尋 **Scala**，然後按一下 [安裝]。
  5. 按一下 [重新啟動 IntelliJ IDEA] 以完成安裝。
  6. 重複步驟 4 和 5 以安裝 [Azure Toolkit for IntelliJ]。 如需詳細資訊，請參閱[安裝 Azure Toolkit for IntelliJ](../azure-toolkit-for-intellij-installation.md)。

## <a name="create-a-spark-scala-application"></a>建立 Spark Scala 應用程式

在本節中，您會使用 IntelliJ IDEA 建立範例 Scala 專案。 在下節中，您要在提交專案之前，將 IntelliJ IDEA 連結至 Hortonworks 沙箱 (模擬器)。

1. 從您的工作站開啟 IntelliJ IDEA。
2. 按一下 [建立新專案]。
3. 按一下左窗格中的 [HDInsight]，按一下右窗格中的 [HDInsight (Scala) 上的 Spark]，然後按 [下一步]。

    ![建立 IntelliJ Scala 專案](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)
4. 輸入以下資訊：

    ![建立 IntelliJ Scala 專案屬性](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)

    - **專案名稱**：提供專案名稱。
    - **專案位置**：提供專案位置。
    - **專案 SDK**︰按一下 [新增]，按一下 [JDK]，然後指定 Java JDK 7 或更新版本的資料夾。  預設位置是 C:\Program Files\Java\jdk1.8.x_xxx。
    - **Scala SDK**︰按一下 [選取]，選取版本 [2.10.6]，然後按一下 [確定]。 如果未列出該版本，請按一下 [下載]，選取 [Scala 版本]，然後按一下 [確定]。 請不要使用版本 2.11.x。 本文章使用 2.10.6 版。
    - **Spark SDK**：下載 [SDK](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409)。 您也可以略過此項，並改用 Spark Maven Repository，不過請確定您已安裝正確的 Maven 儲存機制，以便開發 Spark 應用程式。 (例如，您需要先確定您有安裝 Spark Streaming 組件，如果您使用 Spark Streaming 的話；也請確定您使用標示為 Scala 2.10 的儲存機制 - 請勿使用標示為 Scala 2.11 的儲存機制)。
5. 按一下 [完成] 。
6. 如果並未開啟專案檢視，按一下 **[ALT]+1** 即可開啟。
7. 從 [專案總管] 展開專案，然後按一下[src]。
8. 以滑鼠右鍵按一下 [src]，指向 [新增]，然後按一下 [Scala 類別]。
9. 輸入名稱，選取 [種類] 中的 [物件]，然後按一下 [確定]。

    ![建立新的 IntelliJ Scala 類別](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)
10. 在 .scala 檔案中，貼上下列程式碼：

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

11. 從 [建置] 功能表中，按一下 [建置專案]。 請確定已順利完成編譯。


## <a name="link-to-the-hortonworks-sandbox"></a>連結至 HortonWorks 沙箱

您需要一個現有的 IntelliJ 應用程式，然後才能連結至 Hortonworks 沙箱 (模擬器)。

**若要連結至模擬器**

1. 如果還沒有開啟專案，請在 IntelliJ 中開啟。
2. 從 [檢視] 功能表中，按一下 [工具視窗]，然後按一下 [Azure Explorer]。
3. 展開 [Azure]，以滑鼠右鍵按一下 [HDInsight]，然後按一下 [連結模擬器]。
4. 輸入您為 Hortonworks 沙箱的根帳戶設定的密碼，而其餘的值會和下列螢幕擷取畫面類似，然後按一下 [確定]。 

  ![IntelliJ 連結模擬器](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. 按一下 [是] 來設定模擬器。

  當連線成功時，您可以看到 [HDInsight] 節點底下列出該模擬器 (Hortonworks 沙箱)。

## <a name="submit-the-spark-scala-application-to-the-sandbox"></a>將 Spark Scala 應用程式提交至沙箱

在將 IntelliJ IDEA 連結至模擬器之後，您就可以提交專案。

**若要將專案提交至模擬器**

1. 以滑鼠右鍵按一下 [專案總管] 中的專案名稱，然後按一下 [將 Spark 應用程式提交給 HDInsight]。
2. 指定下列欄位：

    - **Spark 叢集 (僅限 Linux)**︰選取您的本機 Hortonworks 沙箱。
    - **主要類別名稱**︰選擇或輸入主要類別名稱。  在本教學課程中是 **GroupByTest**。
3. 按一下 [提交] 。 Spark 提交工具視窗會顯示工作提交記錄檔。

##<a name="next-steps"></a>後續步驟：

- 若要了解如何使用 HDInsight Tools for IntelliJ 建立 HDInsight 的 Spark 應用程式，請參閱[使用 Azure Toolkit for IntelliJ 中的 HDInsight 工具建立 HDInsight Spark Linux 叢集的 Spark 應用程式](hdinsight-apache-spark-intellij-tool-plugin.md)。
- 若要觀看 HDInsight Tools for IntelliJ 的影片，請參閱[介紹用於開發 Spark 的 HDInsight Tools for IntelliJ](https://mix.office.com/watch/1nqkqjt5xonza)。
- 若要了解如何從遠端使用工具組在 HDInsight 上對 Spark 應用程式進行偵錯，請參閱[在 HDInsight Spark Linux 叢集上從遠端使用 Azure Toolkit for IntelliJ 中的 HDInsight 工具對 Spark 應用程式進行偵錯](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)。
- 若要了解如何使用 HDInsight Tools for Eclipse 建立 Spark 應用程式，請參閱[使用 Azure Toolkit for Eclipse 中的 HDInsight 工具建立 Spark 應用程式](hdinsight-apache-spark-eclipse-tool-plugin.md)。
- 若要觀看 HDInsight Tools for Eclipse 的影片，請參閱[使用 HDInsight Tool for Eclipse 建立 Spark 應用程式](https://mix.office.com/watch/1rau2mopb6fha)。
