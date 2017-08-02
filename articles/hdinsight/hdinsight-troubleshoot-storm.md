---
title: "STORM 疑難排解 - Azure HDInsight | Microsoft Docs"
description: "使用 Storm 常見問題集解答 Azure HDInsight 平台上常見的 Storm 問題。"
keywords: "Azure HDInsight, Storm, 常見問題集, 疑難排解指南, 常見問題"
services: Azure HDInsight
documentationcenter: na
author: raviperi
manager: 
editor: 
ms.assetid: 74E51183-3EF4-4C67-AA60-6E12FAC999B5
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: raviperi
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: b52462096ce977b94ff9514df650607b05e17030
ms.contentlocale: zh-tw
ms.lasthandoff: 07/11/2017

---

# <a name="storm-troubleshooting"></a>STORM 疑難排解

本文描述在 Apache Ambari 中最常發生的 Storm 承載問題及其解決方法。

## <a name="how-do-i-access-storm-ui-on-a-cluster"></a>如何在叢集上存取 Storm UI

### <a name="issue"></a>問題：
有兩種方式可從瀏覽器存取 Storm UI：

#### <a name="ambari-ui"></a>Ambari UI
1. 巡覽至 Ambari 儀表板
1. 從左側的服務清單中選取 [Storm]
1. 從 [快速連結] 下拉式功能表選取 [Storm UI] 選項

#### <a name="direct-link"></a>直接連結
Storm UI 可從 URL 存取：

https://\<叢集 DND 名稱\>/stormui

範例：https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-eventhub-spout-checkpoint-information-from-one-topology-to-another"></a>如何將 Storm EventHub Spout 檢查點資訊從一個拓撲傳輸到另一個拓撲

### <a name="issue"></a>問題：
當使用 HDInsight 的 Storm EventHub Spout Jar 開發讀取自 EventHub 的拓樸時，人員如何在新的叢集中以相同名稱部署拓撲，但在舊的叢集中保留 Zookeeeper 認可的檢查點資料？

#### <a name="where-is-checkpoint-data-stored"></a>檢查點資料的儲存位置
EventHub Spout 會將位移的檢查點資料儲存在兩個根路徑下的 Zookeeper：
- 非交易式 Spout 檢查點會儲存在：/eventhubspout
- 交易式 Spout 檢查點資料會儲存在：/transactional

#### <a name="how-to-restore"></a>還原方式
指令碼和程式庫，從 zookeeper 匯出資料再以新名稱匯回下列位置：https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0。

lib 資料夾的 Jar 檔案包含匯入/匯出作業的實作。
bash 資料夾的範例指令碼，可從舊叢集的 Zookeeper 伺服器匯出資料，再將它匯回新叢集的 Zookeeper 伺服器。

[stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) 指令碼需要從 Zookeeper 節點執行，才能匯入/匯出資料。
指令碼需要更新，才能更正其中的 HDP 版本字串。
(HDInsight 正致力於讓這些指令碼成為泛型，以便可從叢集中任何節點執行，不需要使用者修改)。

匯出命令會將中繼資料寫入指定位置的 HDFS 路徑 (BLOB 或 ADLS 存放區)。

### <a name="examples"></a>範例

##### <a name="export-offset-metadata"></a>匯出位移中繼資料：
1. SSH 匯入舊叢集的 zookeeper 叢集中，該舊叢集需要匯出檢查點位移。
1. (更新 hdp 版本字串之後) 執行以下命令匯出 zookeeper 位移資料並匯入 /stormmetadta/zkdata HDFS 路徑。

```apache   
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
```

##### <a name="import-offset-metadata"></a>匯入位移中繼資料
1. SSH 匯入舊叢集的 zookeeper 叢集中，該舊叢集需要匯出檢查點位移。
1. (更新 hdp 版本字串之後) 執行以下命令將 HDFS 路徑的 /stormmetadata/zkdata 位移資料匯入目標叢集上的 Zookeeper 伺服器。

```apache
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
```
   
##### <a name="delete-offset-metadata-so-topologies-can-start-processing-data-from-either-beginning-or-timestamp-of-user-choice"></a>刪除位移中繼資料，以便拓撲可以從頭開始處理資料，或從使用者選擇的時間戳記開始處理。
1. SSH 匯入舊叢集的 zookeeper 叢集中，該舊叢集需要匯出檢查點位移。
1. (更新 hdp 版本字串之後) 執行以下命令刪除目前叢集的所有 zookeeper 位移資料。

```apache
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>如何找出叢集上的 Storm 二進位檔

### <a name="issue"></a>問題：
 知道 HDInsight 叢集上的 Storm 服務二進位檔位置

### <a name="resolution-steps"></a>解決步驟：

目前 HDP 堆疊的 Storm 二進位檔位於：/usr/hdp/current/storm-client

Headnode 以及背景工作角色節點的這個位置一樣 。
 
/usr/hdp 下可能有多個 HDP 版本的特定二進位檔 (範例： /usr/hdp/2.5.0.1233/storm)

但 /usr/hdp/current/storm-client 是以符號連結在叢集上執行的最新版本。

### <a name="further-reading"></a>進階閱讀：
 [使用 SSH 連線到 HDInsight 叢集](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) [Storm](http://storm.apache.org/)
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>我要如何判斷 Storm 叢集的部署拓撲
 
### <a name="issue"></a>問題：
 
找出所有與 HDInsight Storm 一起安裝的元件。
 
Storm 叢集包含一個 4 節點的類別
1. 閘道器
1. 前端節點
1. Zookeeper 節點
1. 背景工作節點
 
#### <a name="gateway-nodes"></a>閘道節點
是閘道與反向 Proxy 服務，可讓您啟用使用中的 Ambari 管理服務公用存取，處理 Ambari 前置選擇。
 
#### <a name="zookeeper-nodes"></a>Zookeeper 節點
HDInsight 隨附一個 3 節點的 Zookeeper 仲裁。
仲裁大小是固定的且未經設定。
 
在叢集中的 Storm 服務已設定為自動使用 ZK 仲裁。
 
#### <a name="head-nodes"></a>前端節點
Storm 前端節點執行下列服務：
1. Nimbus
1. Ambari 伺服器
1. Ambari 計量伺服器
1. Ambari 計量收集器
 
#### <a name="worker-nodes"></a>背景工作節點
 Storm 背景工作節點執行下列服務：
1. 監督員
1. 執行拓撲的背景工作角色 JVM
1. Ambari 代理程式
 
## <a name="how-do-i-locate-storm-eventhub-spout-binaries-for-development"></a>如何找出用於開發的 Storm-EventHub-Spout 二進位檔
 
### <a name="issue"></a>問題：
如何尋找使用 Storm Eventhub Spout Jar 的詳細資訊，搭配拓樸使用。
 
#### <a name="msdn-articles-on-how-to"></a>MSDN 的操作說明文章
 
##### <a name="java-based-topology"></a>Java 型拓撲
https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology
 
##### <a name="c-based-topology-using-mono-on-hdi-34-linux-storm-clusters"></a>C# 型拓撲 (在 HDI 3.4+ Linux Storm 叢集上使用 Mono)
https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology
 
##### <a name="latest-storm-eventhub-spout-binaries-for-hdi35-linux-storm-clusters"></a>適用於 HDI3.5+ Linux Storm 叢集最新的 Storm EventHub Spout 二進位檔
請參閱 https://github.com/hdinsight/mvn-repo/blob/master/README.md 以了解如何使用最新的 Storm Eventhub Spout 搭配 HDI3.5+ Linux Storm 叢集。
 
##### <a name="source-code-examples"></a>原始程式碼範例：
https://github.com/Azure-Samples/hdinsight-java-storm-eventhub
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>如何找出叢集上的 Storm Log4J 設定檔
 
### <a name="issue"></a>問題：
 
識別 Storm 服務的 Log4J 設定檔。
 
#### <a name="on-headnodes"></a>在 HeadNodes 上：
從 /usr/hdp/<HDPVersion>/storm/log4j2/cluster.xml 可讀取 Nimbus Log4J 設定
 
#### <a name="worker-nodes"></a>背景工作節點
從 /usr/hdp/<HDPVersion>/storm/log4j2/cluster.xml 可讀取監督員的 Log4J 設定
 
從 /usr/hdp/<HDPVersion>/storm/log4j2/worker.xml 可讀取背景工作角色 Log4J 設定
 
範例：/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml







