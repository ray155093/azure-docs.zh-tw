---
title: "Spark 疑難排解 - Azure HDInsight | Microsoft Docs"
description: "使用 Spark 常見問題集解答 Azure HDInsight 平台上常見的 Spark 問題。"
keywords: "Azure HDInsight, Spark, 常見問題集, 疑難排解指南, 常見問題, 應用程式設定, Ambari"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: 25D89586-DE5B-4268-B5D5-CC2CE12207ED
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 7655acd689c3f5b869eb0ddb5f186cff032ca8b6
ms.contentlocale: zh-tw
ms.lasthandoff: 07/11/2017


---

# <a name="spark-troubleshooting"></a>Spark 疑難排解

本文描述在 Apache Ambari 中最常發生的 Spark 承載問題及其解決方法。

## <a name="how-do-i-configure-a-spark-application-through-ambari-on-clusters"></a>如何透過 Ambari 在叢集上設定 Spark 應用程式

### <a name="issue"></a>問題：

需要在 Ambari 中設定 Spark 應用程式可以使用的記憶體數量和核心數目。  

### <a name="resolution-steps"></a>解決步驟： 

此程序設定值之前已在 HDInsight Spark 叢集中設定過。 請參閱[為什麼 Spark 應用程式因為 OutOfMemoryError 失敗？](#why-did-my-spark-application-fail-with-an-outofmemoryerror)判斷需要設定哪些 Spark 設定以及設為哪些值。

1. 從叢集清單中選擇 [Spark2]。

    ![從清單中選取叢集](media/hdinsight-troubleshoot-spark/update-config-1.png)

1. 按一下 [Configs (設定)]  索引標籤。

    ![選擇 [設定] 索引標籤](media/hdinsight-troubleshoot-spark/update-config-2.png)

1. 在設定清單中選擇 [Custom-spark2-defaults]。

    ![選擇 [custom-spark-defaults]](media/hdinsight-troubleshoot-spark/update-config-3.png)

1. 尋找需要調整的設定值，例如 **spark.executor.memory**。 在本例中，值 4608m 太高。

    ![選取 [spark.executor.memory] 欄位](media/hdinsight-troubleshoot-spark/update-config-4.png)

1. 將值設定為 [建議]。 在本例中，這項設定建議值為 2048。

    ![將值變更為 2048m](media/hdinsight-troubleshoot-spark/update-config-5.png)

1. 儲存設定值，然後儲存設定。 

    按一下工具列的 [儲存]  。

    ![儲存設定值和設定](media/hdinsight-troubleshoot-spark/update-config-6a.png)

    如有任何設定需要注意，您會收到通知。 記下它們，然後按一下 [Proceed Anyway] (仍要繼續)。 

    ![按一下 [Proceed Anyway] (仍要繼續)](media/hdinsight-troubleshoot-spark/update-config-6b.png)

    註記設定變更，然後按一下 [儲存]。

    ![鍵入所做變更的附註內容](media/hdinsight-troubleshoot-spark/update-config-6c.png)

1. 每次儲存設定時，系統都會提示您重新啟動服務。 按一下 [重新啟動] 。

    ![按一下 [重新啟動]](media/hdinsight-troubleshoot-spark/update-config-7a.png)

    確認重新啟動。

    ![按一下 [Confirm Reatart All] (確認全部重新啟動)](media/hdinsight-troubleshoot-spark/update-config-7b.png)

    您可以檢閱執行中的處理序。

    ![檢閱執行中的處理序](media/hdinsight-troubleshoot-spark/update-config-7c.png)

1. 您也可以新增設定。 在設定清單中，如步驟 3 選擇 [Custom-spark2-defaults]，然後選擇 [新增屬性]。

    ![按一下 [新增屬性]](media/hdinsight-troubleshoot-spark/update-config-8.png)

1. 定義新的屬性。 您可以用特定設定的對話方塊定義單一屬性，例如資料類型，或者使用一行一定義的方式定義多個屬性。 

    在本例中，**spark.driver.memory** 屬性是以值 4g 定義。

    ![定義新的屬性](media/hdinsight-troubleshoot-spark/update-config-9.png)

1. 儲存設定並重新啟動服務，如步驟 6 和 7 中所述。

這些變更為全叢集的，但可在真正提交 Spark 作業時予以覆寫。

### <a name="further-reading"></a>進階閱讀：

[提交 HDInsight 叢集上的 Spark 作業](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-through-a-jupyter-notebook-on-clusters"></a>如何透過 Jupyter 筆記本在叢集上設定 Spark 應用程式

### <a name="issue"></a>問題：

在 HDInsight 叢集上使用 Jupyter 筆記本時，需要設定 Spark 應用程式可以使用的記憶體數量及核心數目。 

1. 請參閱主題[為什麼 Spark 應用程式因為 OutOfMemoryError 失敗？](#spark-application-failure-outofmemory)判斷需要設定哪些 Spark 設定以及設為哪些值。
1.  在 Jupyter 筆記本第一個資料格中的 %%configure 指示詞後面，以有效的 JSON 格式指定 Spark 設定 (視情況變更實際的值)： 

>![新增設定](media/hdinsight-troubleshoot-spark/add-configuration-cell.png)

### <a name="further-reading"></a>進階閱讀：

[提交 HDInsight 叢集上的 Spark 作業](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-through-livy-on-clusters"></a>如何透過 LIVY 在叢集上設定 Spark 應用程式

### <a name="issue"></a>問題：

需要透過 LIVY 在提交時設定，Spark 應用程式可在 HDInsight 叢集上使用的記憶體數量和核心數目。 

1. 請參閱主題[為什麼 Spark 應用程式因為 OutOfMemoryError 失敗？](#spark-application-failure-outofmemory)判斷需要設定哪些 Spark 設定以及設為哪些值。
1. 使用如 CURL 的 REST 用戶端和類似下面的命令，將 Spark 應用程式提交給 LIVY (視情況變更實際的值)：

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

### <a name="further-reading"></a>進階閱讀：

[提交 HDInsight 叢集上的 Spark 作業](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-through-spark-submit-on-clusters"></a>如何透過 spark-submit 在叢集上設定 Spark 應用程式

### <a name="issue"></a>問題： 

需要透過 spark-submit 在提交時設定，Spark 應用程式可在 HDInsight 叢集上使用的記憶體數量和核心數目。

1. 請參閱主題[為什麼 Spark 應用程式因為 OutOfMemoryError 失敗？](#spark-application-failure-outofmemory)判斷需要設定哪些 Spark 設定以及設為哪些值。
1. 以類似下面的命令啟動 spark-shell (視情況變更實際的設定值)： 

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="further-reading"></a>進階閱讀：

[提交 HDInsight 叢集上的 Spark 作業](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)



## <a name="what-causes-a-spark-application-outofmemoryerror-exception"></a>造成 Spark 應用程式 OutOfMemoryError 例外狀況的原因

### <a name="error"></a>Error:

Spark 應用程式因 OutOfMemoryError 例外狀況而失敗。

### <a name="detailed-description"></a>詳細描述：

Spark 應用程式因下列無法攔截的例外狀況類型而失敗。  
```apache
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439) 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

```apache
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main] 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

### <a name="probable-cause"></a>可能的原因：

此例外狀況最可能發生的原因，就是沒有將足夠的堆積記憶體分配至 Java 虛擬機器 (JVM)；該虛擬機器以執行程式啟動，或是當做 Spark 應用程式的驅動程式。 

### <a name="resolution-steps"></a>解決步驟：

1. 決定 Spark 應用程式將處理的資料大小上限。 您可以依輸入資料的大小上限、由轉換輸入資料所產生的中繼資料，以及進一步轉換中繼資料所產生的輸出資料粗略估算。 若無法進行正式的初始估算，也可以設定為反覆的程序。 
1. 請確定要使用的 HDInsight 叢集具有足夠的記憶體資源及核心，才能採用 Spark 應用程式。 您可以檢視叢集之 YARN UI 的叢集計量一節，查看已使用記憶體和記憶體總計，以及已使用的 VCore 和VCore 總計的值。

1. 將下列 Spark 設定設為適當的值，不要超過可用記憶體及核心的 90%；以 YARN 的檢視結果來看，這些值都在 Spark 應用程式的記憶體需求範圍內： 

```apache
spark.executor.instances (Example: 8 for 8 executor count) 
spark.executor.memory (Example: 4g for 4 GB) 
spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
spark.executor.cores (Example: 2 for 2 cores per executor) 
spark.driver.memory (Example: 8g for 8GB) 
spark.driver.cores (Example: 4 for 4 cores)   
spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
```

所有執行程式使用的記憶體總計 = 
```apache
spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
```
驅動程式使用的記憶體總計 = 
```apache
spark.driver.memory + spark.yarn.driver.memoryOverhead
```

### <a name="further-reading"></a>進階閱讀：

- [Spark 記憶體管理概觀](http://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [Debugging Spark application on HDInsight clusters](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/) (在 HDInsight 叢集中偵錯 Spark 應用程式)










