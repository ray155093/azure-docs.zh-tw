---
title: "YARN 疑難排解 - Azure HDInsight | Microsoft Docs"
description: "使用 Yarn 常見問題集解答 Azure HDInsight 平台上常見的 Yarn 問題。"
keywords: "Azure HDInsight, Yarn, 常見問題集, 疑難排解指南, 常見問題"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: F76786A9-99AB-4B85-9B15-CA03528FC4CD
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 576d6bfe9697d2abd6f84824960dad62e9ed3c11
ms.contentlocale: zh-tw
ms.lasthandoff: 07/11/2017


---

# <a name="yarn-troubleshooting"></a>YARN 疑難排解

本文描述在 Apache Ambari 中最常發生的 YARN 承載問題及其解決方法。

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>如何在叢集上建立新的 Yarn 佇列

### <a name="issue"></a>問題：

建立在 HDInsight 叢集上配置容量的新 Yarn 佇列。  

### <a name="resolution-steps"></a>解決步驟： 

透過 Ambari 使用下列步驟建立新的 Yarn 佇列，並平衡所有佇列之間的容量配置。 

在本例中，兩個現有的佇列 (預設和 thriftsvr) 都從 50% 的容量變更為 25% 的容量，以便新的佇列 (Spark) 擁有 50% 的容量。
| 佇列 | 容量 | 最大 容量 |
| --- | --- | --- | --- |
| 預設值 | 25% | 50% |
| thrftsvr | 25% | 50% |
| spark | 50% | 50% |
1. 按一下 Abari 檢視圖示和格線模式，然後選擇 [Yarn Queue Manager] (Yarn 佇列管理員)。

    ![按一下 Ambari 檢視圖示](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
1. 選取 [預設] 佇列。

    ![選取預設佇列](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
1. 將**預設**佇列的 [容量] 從 50% 變更成 25%，也將 **thriftsvr** 佇列的 [容量] 變更為 25%。

    ![將預設和 thriftsvr 佇列的容量變更為 25%](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
1. 按一下 [新增佇列] 建立新的佇列。

    ![按一下 [新增佇列]](media/hdinsight-troubleshoot-yarn/create-queue-4.png)
1. 命名新的佇列。

    ![將佇列命名為 Spark](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  
1. 將 [容量] 值保持在 50%，然後按一下 [動作] 按鈕。

    ![按一下 [動作] 按鈕](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
1. 選擇 [Save and Refresh Queues] (儲存並重新整理佇列)。

    ![選擇 [Save and Refresh Queues] (儲存並重新整理佇列)](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

這些變更都會立即顯示在 Yarn 排程器 UI 中。

### <a name="further-reading"></a>進階閱讀：

- [Yarn 容量排程器](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)


## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>如何下載叢集的 Yarn 記錄檔

#### <a name="issue"></a>問題：

需要從 HDInsight 叢集下載 Yarn 應用程式主機和其他容器記錄。  

#### <a name="resolution-steps"></a>解決步驟： 

1. 使用安全殼層 (SSH) 用戶端連接到 HDInsight 叢集 (請見後文＜進階閱讀＞一節)。
1. 列出目前使用下列命令執行 Yarn 應用程式的所有應用程式識別碼：

```apache
yarn top
```
列在要下載記錄檔之 `APPLICATIONID` 資料行中的識別碼。

```apache
YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                  APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
 application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
 application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
```

使用下列命令下載所有應用程式主機的 Yarn 容器記錄：
   
```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
```

這會以文字格式建立名為 `amlogs.txt` 的記錄檔。 

使用下列命令下載僅有最新應用程式主機的 Yarn 容器記錄：

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
```

這會以文字格式建立名為 `latestamlogs.txt` 的記錄檔。 

使用下列命令下載前兩部應用程式主機的 Yarn 容器記錄：

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt 
```

這會以文字格式建立名為 `first2amlogs.txt` 的記錄檔。 

使用下列命令下載所有 Yarn 容器記錄：

```apache
yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
```

這會以文字格式建立名為 `logs.txt` 的記錄檔。 

使用下列命令下載特定容器的 Yarn 容器記錄：

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt 
```

這會以文字格式建立名為 `containerlogs.txt` 的記錄檔。

#### <a name="further-readings"></a>進階閱讀：

- [使用 SSH 連線到 HDInsight 叢集](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)

- [Apache Hadoop Yarn 概念與應用程式](https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/)









