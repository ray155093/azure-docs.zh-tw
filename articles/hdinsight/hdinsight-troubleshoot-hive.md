---
title: "HIVE 疑難排解 - Azure HDInsight | Microsoft Docs"
description: "使用 Hive 常見問題集解答 Azure HDInsight 平台上常見的 Hive 問題。"
keywords: "Azure HDInsight, Hive, 常見問題集, 疑難排解指南, 常見問題"
services: Azure HDInsight
documentationcenter: na
author: dharmeshkakadia
manager: 
editor: 
ms.assetid: 15B8D0F3-F2D3-4746-BDCB-C72944AA9252
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: dharmeshkakadia
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 67b096c0585f7b73a57b784683944052d1cfaa33
ms.contentlocale: zh-tw
ms.lasthandoff: 07/11/2017


---

# <a name="hive-troubleshooting"></a>HIVE 疑難排解

本文描述在 Apache Ambari 中最常發生的 Hive 承載問題及其解決方法。

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>如何匯出 Hive 中繼存放區並匯入另一個叢集

### <a name="issue"></a>問題：

需要匯出 Hive 中繼存放區並匯入另一個 HDInsight 叢集。  

### <a name="resolution-steps"></a>解決步驟： 

1. 使用安全殼層 (SSH) 用戶端連線到 HDInsight 叢集 (請見下列的＜進階閱讀＞一節)。
1. 在您要匯出中繼存放區的 HDInsight 叢集上執行下列命令：

```apache
for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
```

這會產生名為 `allatables.sql` 的檔案。

- 將 `alltables.sql` 檔案複製到 HDInsight 叢集，並執行下列命令：

```apache
hive -f alltables.sql
```

此程式碼會假設新叢集上的資料路徑與舊叢集相同。 若非如此，您可以手動編輯產生的  
`alltables.sql` 檔案以反映任何變更。

### <a name="further-reading"></a>進階閱讀：

- [使用 SSH 連線到 HDInsight 叢集](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)


## <a name="how-do-locate-hive-logs-on-a-cluster"></a>如何在叢集上尋找 Hive 記錄

### <a name="issue"></a>問題：

需要在 HDInsight 叢集上尋找 Hive 用戶端、中繼存放區和 Hiveserver 記錄。  

### <a name="resolution-steps"></a>解決步驟： 

- 使用安全殼層 (SSH) 用戶端連線到 HDInsight 叢集 (請見下列的＜進階閱讀＞一節)。
- Hive 用戶端記錄可以在下列位置找到：

```apache
/tmp/<username>/hive.log 
```

- Hive 中繼存放區記錄可以在下列位置找到：

```apache
/var/log/hive/hivemetastore.log 
```

- Hiveserver 記錄可以在下列位置找到：

```apache
/var/log/hive/hiveserver2.log 
```

### <a name="further-reading"></a>進階閱讀：

- [使用 SSH 連線到 HDInsight 叢集](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)

## <a name="how-do-i-launch-hive-shell-with-specific-configurations-on-a-cluster"></a>如何在叢集上以特定設定啟動 Hive Shell

### <a name="issue"></a>問題：

需要在 HDInsight 叢集上覆寫或指定啟動時的 Hive Shell 設定。  

### <a name="resolution-steps"></a>解決步驟： 

- 在啟動 Hive Shell 時，指定設定機碼值組 (請見下列的＜進階閱讀＞一節)：

```apache
hive -hiveconf a=b 
```

- 使用下列命令，列出 Hive Shell 上所有有效的設定：

```apache
hive> set;
```

例如，使用下列命令，以啟動 Hive Shell 並在主控台上啟用偵錯記錄：
             
```apache
hive -hiveconf hive.root.logger=ALL,console 
```

### <a name="further-reading"></a>進階閱讀：

- [Hive 設定屬性](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)


## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>如何分析叢集關鍵路徑上的 Tez DAG 資料

### <a name="issue"></a>問題：

需要分析 Tez 有向非循環圖 (DAG) 的資訊，特別是 HDInsight 叢集上的關鍵路徑

### <a name="resolution-steps"></a>解決步驟：
 
- 使用安全殼層 (SSH) 用戶端連線到 HDInsight 叢集 (請見下列的＜進階閱讀＞一節)。

- 在命令提示字元中執行下列命令：
   
```apache
hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
```

- 使用下列命令，列出可用來分析 Tez DAG 的其他分析器：

```apache
hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
```

必須指定範例程式作為第一個引數。

有效的程式名稱如下：ContainerReuseAnalyzer：列印 DAG 中的容器重複使用詳細資料 CriticalPath：尋找 DAG 的關鍵路徑 LocalityAnalyzer：列印 DAG 中的位置詳細資料 ShuffleTimeAnalyzer：分析 DAG 中的隨機播放時間詳細資料 SkewAnalyzer：分析 DAG 中的扭曲詳細資料 SlowNodeAnalyzer：列印 DAG 中的節點詳細資料 SlowTaskIdentifier：列印 DAG 中的低速工作詳細資料 SlowestVertexAnalyzer：列印 DAG 中最慢頂點詳細資料 SpillAnalyzer：列印 DAG 中的溢出詳細資料 TaskConcurrencyAnalyzer：列印 DAG 中的工作並行詳細資料 VertexLevelCriticalPathAnalyzer：在 DAG 的頂點層級中尋找關鍵路徑


### <a name="further-reading"></a>進階閱讀：

- [使用 SSH 連線到 HDInsight 叢集](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)



## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>如何從叢集下載 Tez DAG 資料

#### <a name="issue"></a>問題：

需要從 HDInsight 叢集下載 Tez 有向非循環圖 (DAG) 資訊。

#### <a name="resolution-steps"></a>解決步驟：

有兩種方式可以收集 Tez DAG 資料。

- 從命令列：
 
    使用安全殼層 (SSH) 用戶端連線到 HDInsight 叢集。 在命令提示字元中執行下列命令：
   
```apache
hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId> 
```

- 或者，您可以使用 Ambari Tez 檢視：
   
請移至 Ambari--> 移至 Tez 檢視 (隱藏在右上角的磚圖示) --> 按一下您感興趣的 DAG --> 按一下 [下載資料]。

#### <a name="further-reading"></a>進階閱讀：

1) [使用 SSH 連線到 HDInsight 叢集](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)








