---
title: "HBase 疑難排解 - Azure HDInsight | Microsoft Docs"
description: "疑難排解區域鏈結中出現漏洞的原因。"
services: hdinsight
documentationcenter: 
author: nitinver
manager: ashitg
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 7/7/2017
ms.author: nitinver
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 29b1776d6b0c456515738aae3c5fd836c9a0295d
ms.contentlocale: zh-tw
ms.lasthandoff: 07/11/2017

---

# <a name="hbase-troubleshooting"></a>HBASE 疑難排解

本文描述在 Apache Ambari 中最常發生的 HBASE 承載問題及其解決方法。

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>如何執行回報多個未指派區域的 hbck 命令

當 HBase 使用者執行 'hbase hbck' 命令時，看到「多個區域未獲指派或區域鏈結中出現漏洞」是一個常見的問題。

使用者會從 HBase Master UI 看到所有區域伺服器中不對稱區域的計數。 在這之後，使用者可以執行 'hbase hbck' 命令，並應該會注意到區域鏈結中的漏洞。

因為漏洞可能是由於這些離線區域所導致，所以使用者應該先修正指派。 

請遵循下列步驟，讓未指派的區域恢復正常狀態：

1. 使用 SSH 登入 HDInsight HBase 叢集。
1. 執行 'hbase zkcli' 命令以與 Zookeeper Shell 連線。
1. 執行 'rmr /hbase/regions-in-transition' 或 'rmr /hbase-unsecure/regions-in-transition' 命令。
1. 使用 'exit' 命令來結束 'hbase zkcli' Shell。
1. 開啟 Ambari UI，並從 Ambari 重新啟動 Active HBase Master 服務。
1. 再次執行 'hbase hbck' 命令 (但不含任何進一步選項)。

檢查步驟 6 中的命令輸出，並確定所有區域已獲指派。

---

## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>如何修正區域指派的 hbck 命令逾時問題

### <a name="probable-cause"></a>可能的原因

這裡的可能原因或許是數個區域長時間處於「轉換中」狀態。 這些區域在 HBase Master UI 中可視為離線。 由於有大量的區域嘗試進行轉換，HBase Master 可能會逾時，因而無法讓這些區域恢復線上狀態。

### <a name="resolution-steps"></a>解決步驟

以下是修正 hbck 逾時問題的步驟：

1. 使用 SSH 登入 HDInsight HBase 叢集。
1. 執行 'hbase zkcli' 命令以與 Zookeeper Shell 連線。
1. 執行 'rmr /hbase/regions-in-transition' 或 'rmr /hbase-unsecure/regions-in-transition' 命令。
1. 使用 'exit' 命令來結束 'hbase zkcli' Shell。
1. 開啟 Ambari UI，並從 Ambari 重新啟動 Active HBase Master 服務。
1. 再次執行 'hbase hbck -fixAssignments' 命令，它應該不會再逾時。

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-an-cluster"></a>如何在叢集中強制停用 HDFS 安全模式

### <a name="issue"></a>問題：

本機 HDFS 在 HDInsight 叢集上卡在安全模式中。   

### <a name="detailed-description"></a>詳細描述：

無法執行簡單的 HDFS 命令，如下所示：

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

嘗試執行上述命令時發生的錯誤如下：

```apache
hdiuser@hn0-spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.mkdirs(FSNamesystem.java:4010)
        at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.mkdirs(NameNodeRpcServer.java:1102)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.mkdirs(ClientNamenodeProtocolServerSideTranslatorPB.java:630)
        at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:640)
        at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:982)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2313)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2309)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:422)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1724)
        at org.apache.hadoop.ipc.Server$Handler.run(Server.java:2307)
        at org.apache.hadoop.ipc.Client.getRpcResponse(Client.java:1552)
        at org.apache.hadoop.ipc.Client.call(Client.java:1496)
        at org.apache.hadoop.ipc.Client.call(Client.java:1396)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Invoker.invoke(ProtobufRpcEngine.java:233)
        at com.sun.proxy.$Proxy10.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolTranslatorPB.mkdirs(ClientNamenodeProtocolTranslatorPB.java:603)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invokeMethod(RetryInvocationHandler.java:278)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:194)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:176)
        at com.sun.proxy.$Proxy11.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.DFSClient.primitiveMkdir(DFSClient.java:3061)
        at org.apache.hadoop.hdfs.DFSClient.mkdirs(DFSClient.java:3031)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1162)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1158)
        at org.apache.hadoop.fs.FileSystemLinkResolver.resolve(FileSystemLinkResolver.java:81)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirsInternal(DistributedFileSystem.java:1158)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirs(DistributedFileSystem.java:1150)
        at org.apache.hadoop.fs.FileSystem.mkdirs(FileSystem.java:1898)
        at org.apache.hadoop.fs.shell.Mkdir.processNonexistentPath(Mkdir.java:76)
        at org.apache.hadoop.fs.shell.Command.processArgument(Command.java:273)
        at org.apache.hadoop.fs.shell.Command.processArguments(Command.java:255)
        at org.apache.hadoop.fs.shell.FsCommand.processRawArguments(FsCommand.java:119)
        at org.apache.hadoop.fs.shell.Command.run(Command.java:165)
        at org.apache.hadoop.fs.FsShell.run(FsShell.java:297)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:90)
        at org.apache.hadoop.fs.FsShell.main(FsShell.java:350)
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

### <a name="probable-cause"></a>可能的原因：

HDInsight 叢集已調降為低於或接近 HDFS 複寫因數的極少數節點。

### <a name="resolution-steps"></a>解決步驟： 

- 使用下列命令報告 HDInsight 叢集上的 HDFS 狀態：

```apache
hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
```

```apache
hdiuser@hn0-spark2:~$ hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
Safe mode is ON
Configured Capacity: 3372381241344 (3.07 TB)
Present Capacity: 3138625077248 (2.85 TB)
DFS Remaining: 3102710317056 (2.82 TB)
DFS Used: 35914760192 (33.45 GB)
DFS Used%: 1.14%
Under replicated blocks: 0
Blocks with corrupt replicas: 0
Missing blocks: 0
Missing blocks (with replication factor 1): 0

-------------------------------------------------
Live datanodes (8):

Name: 10.0.0.17:30010 (10.0.0.17)
Hostname: 10.0.0.17
Decommission Status : Normal
Configured Capacity: 421547655168 (392.60 GB)
DFS Used: 5288128512 (4.92 GB)
Non DFS Used: 29087272960 (27.09 GB)
DFS Remaining: 387172253696 (360.58 GB)
DFS Used%: 1.25%
DFS Remaining%: 91.85%
Configured Cache Capacity: 0 (0 B)
Cache Used: 0 (0 B)
Cache Remaining: 0 (0 B)
Cache Used%: 100.00%
Cache Remaining%: 0.00%
Xceivers: 2
Last contact: Wed Apr 05 16:22:00 UTC 2017
...

```
- 您也可以使用下列命令，檢查 HDInsight 叢集上的 HDFS 完整性：

```apache
hdiuser@hn0-spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
```

```apache
Connecting to namenode via http://hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net:30070/fsck?ugi=hdiuser&path=%2F
FSCK started by hdiuser (auth:SIMPLE) from /10.0.0.22 for path / at Wed Apr 05 16:40:28 UTC 2017
....................................................................................................

....................................................................................................
..................Status: HEALTHY
 Total size:    9330539472 B
 Total dirs:    37
 Total files:   2618
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2535 (avg. block size 3680686 B)
 Minimally replicated blocks:   2535 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     3.0
 Corrupt blocks:                0
 Missing replicas:              0 (0.0 %)
 Number of data-nodes:          8
 Number of racks:               1
FSCK ended at Wed Apr 05 16:40:28 UTC 2017 in 187 milliseconds


The filesystem under path '/' is HEALTHY
```

- 如果確定沒有遺漏、損毀或複寫中的區塊，或是這些區塊可以予以忽略，請執行下列命令使名稱節點脫離安全模式：

```apache
hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>如何修正與 Apache Phoenix 的 JDBC 或 sqlline 連線問題

### <a name="resolution-steps"></a>解決步驟：

若要與 Apache Phoenix 連線，您必須提供使用中 Zookeeper 節點的 IP。 請確定 sqlline.py 嘗試連線的 Zookeeper 服務已啟動並執行。
1. 以 SSH 方式登入 HDInsight 叢集。
1. 嘗試使用下列命令：
        
```apache
        "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
```     
    Note: The IP of Active Zooker node can be identified from Ambari UI, by following the links to HBase -> "Quick Links" -> "ZK* (Active)" -> "Zookeeper Info". 

如果 sqlline.py 連線至 Apache Phoenix，而且不會逾時，請執行下列命令以驗證 Apache Phoenix 的可用性和健康狀態：

```apache
        !tables
        !quit
```      
- 如果上述命令能夠運作，就表示沒有問題。 使用者提供的 IP 可能不正確。
   
    然而，如果此命令暫停太久，則會擲回下方提及的錯誤，請繼續遵循下列疑難排解指南：

```apache
        Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
```

- 從前端節點 (hn0) 執行下列命令，以診斷 Phoenix SYSTEM.CATALOG 資料表的狀況：

```apache
        hbase shell
        
        count 'SYSTEM.CATALOG'
```        
- 此命令應該傳回的錯誤大致如下： 

```apache
        ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
```
- 從 Ambari UI 遵循下列步驟，在所有 Zookeeper 節點上重新啟動 HMaster 服務：

    a. 移至 HBase [摘要] 區段中的 [HBase] -> [Active HBase Master] 連結。 
    a. 在 [元件] 區段中，重新啟動 HBase Master 服務。
    a. 針對其餘的 Standby HBase Master 服務，重複上述步驟。 

HBase Master 服務可能需要五分鐘的時間，才能穩定和完成復原。 等候幾分鐘之後，重複 sqlline.py 命令，以確認該系統目錄資料表已啟動且可供查詢。 

只要 'SYSTEM.CATALOG' 資料表回復正常狀態，Apache Phoenix 的連線問題應該就會自動解決。


## <a name="what-causes-a-master-server-to-fail-to-start"></a>什麼情況導致主要伺服器無法啟動

### <a name="error"></a>Error: 

不可部分完成的重新命名失敗

### <a name="detailed-description"></a>詳細描述：

在啟動過程中，HMaster 執行了許多初始化步驟，包括將資料從臨時 (.tmp) 資料夾移至數據資料夾；同時查詢 WAL (預寫記錄檔) 資料夾，以查看是否有任何無效區域伺服器等等。 

在所有這些情況下，它會對這些資料夾執行基本的 'list' 命令。 如果任何時間它在任一資料夾中看到未預期的檔案，則會擲回例外狀況，因此不會啟動。  

### <a name="probable-cause"></a>可能的原因：

嘗試找出檔案建立的時間表，並查看區域伺服器記錄中該時間前後是否存在處理序當機 (請連絡任何 HBase 工作人員來協助您執行此動作)。 這可協助我們提供更健全的機制，以避免發生這個錯誤，並確保處理序順利關閉。

### <a name="resolution-steps"></a>解決步驟：

在這種情況下，請嘗試檢查呼叫堆疊，並查看哪一個資料夾可能會造成問題 (例如，它是 WAL 資料夾或 .tmp 資料夾)。 接著，透過 Cloud Explorer 或透過 hdfs 命令嘗試找出問題檔案 - 這通常是 *-renamePending.json 檔案 (用來在 WASB 驅動程式中實作不可部分完成之重新命名作業的日誌檔案；由於此實作中發生錯誤，這類檔案可能因處理序當機等情況而保留下來)。 也請透過 Cloud Explorer 強制刪除此項目。 

此外，這個位置有時候還可能有本質為 $$$.$$$ 的暫存檔，這個檔案只能透過 hdfs ls 命令看到，而無法透過 Cloud Explorer 看到。 您可以使用 hdfs 命令 "hdfs dfs -rm /<the path>/\$\$\$.\$\$\$" 來刪除此檔案。  

一旦這麼做，HMaster 應立即啟動。 

### <a name="error-no-server-address-listed-in"></a>錯誤：其中未列出任何伺服器位址 

區域 xxx 的 hbase: meta 中未列出任何伺服器位址

### <a name="detailed-description"></a>詳細描述：

客戶在其 Linux 叢集上遇到該 hbase: meta 資料表不在線上的問題。 執行 hbck 時回報 "hbase: meta table replicaId 0 is not found on any region" (在任何區域上找不到 hbase: meta 資料表 replicaId 0)。 重新啟動 HBase 之後，徵兆變成 HMaster 無法初始化。 HMaster 記錄中報告 "No server address listed in hbase: meta for region hbase: backup <region name>" (區域 hbase: backup &lt;region name&gt; 的 hbase: meta 中未列出任何伺服器位址)。  

### <a name="resolution-steps"></a>解決步驟：

- 在 HBase Shell 上輸入下列命令 (視情況變更實際值)：  

```apache
> scan 'hbase:meta'  
```

```apache
> delete 'hbase:meta','hbase:backup <region name>','<column name>'  
```

- 刪除 hbase: namespace 的項目，因為掃描 hbase: namespace 資料表時可能會報告相同的錯誤。

- 從 Ambari UI 重新啟動使用中的 HMaster，使 HBase 處於執行中狀態。  

- 在 HBase Shell 上執行下列命令，以啟動所有離線資料表：

```apache 
hbase hbck -ignorePreCheckPermission -fixAssignments 
```

### <a name="further-reading"></a>進階閱讀：

[無法處理 HBase 資料表](http://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Error:

HMaster 逾時，並發生嚴重例外狀況，如 java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned (等待指派命名空間資料表時，逾時 300000 毫秒)

### <a name="detailed-description"></a>詳細描述：

客戶很明顯有許多資料表和區域，但在重新啟動其 HMaster 服務時並未排清，就會發生此問題。 重新啟動因上述訊息而失敗。 沒有找到其他錯誤。  

### <a name="probable-cause"></a>可能的原因：

這是 HMaster 的已知「缺失」- 一般叢集啟動工作可能需要很長的時間，但 HMaster 因為尚未指派命名空間資料表而關閉 - 其只會在這個存在大量未排清資料的情況下發生，因此五分鐘的逾時並不足夠
  
### <a name="resolution-steps"></a>解決步驟：

- 存取 Ambari UI，移至 [HBase]-> [設定]，在自訂的 hbase-site.xml 中新增下列設定： 

```apache
Key: hbase.master.namespace.init.timeout Value: 2400000  
```

- 重新啟動必要的服務 (主要是 HMaster，以及其他可能的 HBase 服務)。  



## <a name="what-causes-a-restart-failure-on-a-region-server"></a>什麼情況導致區域伺服器上的重新啟動失敗

### <a name="probable-cause"></a>可能的原因：

首先，這種情況可透過遵循最佳做法來加以預防。 建議您在計劃重新啟動 HBase 區域伺服器時暫停工作負載過重的活動。 如果進行關機時應用程式繼續與區域伺服器連線，這會使區域伺服器重新啟動作業變慢幾分鐘。 此外，建議使用者排清所有資料表，請檢閱 [HDInsight HBase：如何透過排清資料表改善 HBase 叢集重新啟動時間](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)作為參考。

如果使用者從 Ambari UI 對 HBase 區域伺服器起始重新啟動作業， 他會立即看到區域伺服器關機，但不久之後便會回復為啟動狀態。 

以下是幕後發生的情況： 

1. Ambari 代理程式會傳送停止要求給區域伺服器。
1. 接著，Ambari 代理程式會等待 30 秒的時間，讓區域伺服器正常關機。 
1. 如果客戶的應用程式繼續與區域伺服器連線，則不會立即關機，因此 30 秒逾時將會很快過期。 
1. 30 秒到期後，Ambari 代理程式會將強制終止 (kill-9) 傳送至區域伺服器。 在 ambari-agent 記錄 (位於個別背景工作節點的 /var/log/ 目錄內) 中可以觀察到這一點，如下所示：

```apache
         2017-03-21 13:22:09,171 - Execute['/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/current/hbase-regionserver/conf stop regionserver'] {'only_if': 'ambari-sudo.sh  -H -E t
         est -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1', 'on_timeout': '! ( ambari-sudo.sh  -H -E test -
         f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H 
         -E cat /var/run/hbase/hbase-hbase-regionserver.pid`', 'timeout': 30, 'user': 'hbase'}
         2017-03-21 13:22:40,268 - Executing '! ( ambari-sudo.sh  -H -E test -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >
         /dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid`'. Reason: Execution of 'ambari-sudo.sh su hbase -l -s /bin/bash -c 'export  
         PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/var/lib/ambari-agent ; /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/curre
         nt/hbase-regionserver/conf stop regionserver was killed due timeout after 30 seconds
         2017-03-21 13:22:40,285 - File['/var/run/hbase/hbase-hbase-regionserver.pid'] {'action': ['delete']}
         2017-03-21 13:22:40,285 - Deleting File['/var/run/hbase/hbase-hbase-regionserver.pid']
```
由於突然關機之故，即使區域伺服器處理序已終止，也可能不會釋放與處理序建立關聯的連接埠。 這種情況可能導致在啟動區域伺服器時發生 AddressBindException，如下列記錄中所示。 在 region-server.log (位於區域伺服器啟動失敗的背景工作節點的 /var/log/hbase 目錄內) 中可以確認這一點。 

```apache

       2017-03-21 13:25:47,061 ERROR [main] regionserver.HRegionServerCommandLine: Region server exiting
       java.lang.RuntimeException: Failed construction of Regionserver: class org.apache.hadoop.hbase.regionserver.HRegionServer
       at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2636)
       at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.start(HRegionServerCommandLine.java:64)
       at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.run(HRegionServerCommandLine.java:87)
       at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
       at org.apache.hadoop.hbase.util.ServerCommandLine.doMain(ServerCommandLine.java:126)
       at org.apache.hadoop.hbase.regionserver.HRegionServer.main(HRegionServer.java:2651)
       
       Caused by: java.lang.reflect.InvocationTargetException
       at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
       at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)
       at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
       at java.lang.reflect.Constructor.newInstance(Constructor.java:526)
       at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2634)
       ... 5 more
       
       Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
       at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2497)
       at org.apache.hadoop.hbase.ipc.RpcServer$Listener.<init>(RpcServer.java:580)
       at org.apache.hadoop.hbase.ipc.RpcServer.<init>(RpcServer.java:1982)
       at org.apache.hadoop.hbase.regionserver.RSRpcServices.<init>(RSRpcServices.java:863)
       at org.apache.hadoop.hbase.regionserver.HRegionServer.createRpcServices(HRegionServer.java:632)
       at org.apache.hadoop.hbase.regionserver.HRegionServer.<init>(HRegionServer.java:532)
       ... 10 more
       
       Caused by: java.net.BindException: Address already in use
       at sun.nio.ch.Net.bind0(Native Method)
       at sun.nio.ch.Net.bind(Net.java:463)
       at sun.nio.ch.Net.bind(Net.java:455)
       at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)
       at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)
       at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2495)
       ... 15 more
```

### <a name="resolution-steps"></a>解決步驟：

在這種情況下，您可以嘗試下列因應措施： 

- 在起始重新啟動之前，嘗試減少 HBase 區域伺服器的負載。 

- 或者 (如果上述步驟沒有作用)，請嘗試使用下列命令，手動重新啟動背景工作節點上的區域伺服器：

```apache
      sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
      sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
```



