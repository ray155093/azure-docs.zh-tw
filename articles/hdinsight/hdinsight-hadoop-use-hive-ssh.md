---
title: "在 HDInsight (Hadoop) 中使用 Hive 殼層 | Microsoft Docs"
description: "了解如何使用 Hive 殼層搭配以 Linux 為基礎的 HDInsight 叢集。 您將了解如何使用 SSh 來連線到 HDInsight 叢集，然後使用 Hive 殼層執行互動式查詢。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 0e919171-03e6-4f5a-ab4e-3eec3e34c347
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 237b78f723fe6dbb800f9e0be2314c7c8a5c668e
ms.lasthandoff: 03/25/2017


---
# <a name="use-hive-with-hadoop-in-hdinsight-with-ssh"></a>利用 SSH 搭配使用 Hive 與 HDInsight 中的 Hadoop
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

在本文中，您將學習如何使用安全殼層 (SSH) 連線至 Azure HDInsight 叢集上的 Hadoop，然後使用 Hive 命令列介面 (CLI) 以互動方式提交 Hive 查詢。

> [!IMPORTANT]
> 雖然 HDInsight 叢集上有提供 Hive 命令，但是您應該考慮使用 Beeline。 Beeline 是可搭配 Hive 使用的較新用戶端，隨附於您的 HDInsight 叢集中。 如需有關如何使用它的詳細資訊，請參閱 [使用 Beeline 搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-hadoop-use-hive-beeline.md)。


## <a id="prereq"></a>必要條件
若要完成本文中的步驟，您需要下列項目：

* HDInsight 叢集上以 Linux 為基礎的 Hadoop。

  > [!IMPORTANT]
  > Linux 是 HDInsight 3.4 版或更新版本上唯一使用的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 取代](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)。

* SSH 用戶端。 Linux、Unix 和 Mac OS 應該具備 SSH 用戶端。 Windows 使用者必須下載用戶端，例如 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

## <a id="ssh"></a>使用 SSH 連線
使用 SSH 命令，連線至 HDInsight 叢集的完整網域名稱 (FQDN)。 FQDN 將是您提供給叢集的名稱，然後是 **.azurehdinsight.net**。 例如，下列命令會連線至名為 **myhdinsight**的叢集：

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**如果您提供憑證金鑰進行 SSH 驗證** (在建立 HDInsight 叢集時)，可能需要指定用戶端系統上私密金鑰的位置：

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**如果您提供密碼進行 SSH 驗證** (在建立 HDInsight 叢集時)，則需要在出現提示時提供密碼。

如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a id="hive"></a>使用 Hive 命令
1. 連線之後，使用下列命令來啟動 Hive CLI：
   
        hive
2. 使用 CLI，輸入下列陳述式，以使用範例資料來建立名為 **log4jLogs** 的新資料表：
   
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   
    這些陳述式會執行下列動作：
   
   * **DROP TABLE** - 刪除資料表和資料檔 (如果資料表已存在)。
   * **CREATE EXTERNAL TABLE** - 在 Hive 中建立新的「外部」資料表。 外部資料表只會將資料表定義儲存在 Hive 中。 資料會留在原來的位置。
   * **ROW FORMAT** - 告訴 Hive 如何格式化資料。 在此情況下，每個記錄中的欄位會以空格隔開。
   * **STORED AS TEXTFILE LOCATION** - 將資料的儲存位置告訴 Hive (example/data 目錄)，且資料儲存為文字。
   * **SELECT** - 選擇其資料欄 **t4** 包含值 **[ERROR]** 的所有資料列計數。 這應該會傳回值 **3** ，因為有 3 個資料列包含此值。
   * **INPUT__FILE__NAME LIKE '%.log'** - 告訴 Hive 我們只應該從檔名以 log 結尾的檔案中傳回資料。 這將限制包含此資料的 sample.log 檔案搜尋，對於不符合我們所定義結構描述的其他範例資料檔案，會防止其傳回資料。
     
     > [!NOTE]
     > 當您預期以外部來源更新基礎資料 (例如自動化資料上傳程序)，或以其他 MapReduce 作業更新基礎資料，但希望 Hive 查詢一律使用最新資料時，必須使用外部資料表。
     > 
     > 捨棄外部資料表並 **不會** 刪除資料，只會刪除資料表定義。
     > 
     > 
3. 使用下列陳述式來建立名為 **errorLogs**的新「內部」資料表：
   
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   
    這些陳述式會執行下列動作：
   
   * **CREATE TABLE IF NOT EXISTS** - 建立資料表 (如果不存在)。 因為未使用 **EXTERNAL** 關鍵字，所以這是內部資料表，而內部資料表儲存在 Hive 資料倉儲中，並完全透過 Hive 所管理。
   * **STORED AS ORC** - 以最佳化資料列單欄式 (Optimized Row Columnar, ORC) 格式儲存資料。 這是高度最佳化且有效率的 Hive 資料儲存格式。
   * **INSERT OVERWRITE ...SELECT**- 從包含 **[ERROR]** 的 **log4jLogs** 資料表選取資料列，然後將資料插入 **errorLogs** 資料表。
     
     若要確認是否只有資料欄 t4 中包含 **[ERROR]** 的資料列會儲存至 **errorLogs** 資料表，請使用下列陳述式，從 **errorLogs** 傳回所有資料列：
     
       SELECT * from errorLogs;
     
     應該傳回三個資料列，且在資料行 t4 中全部包含 **[ERROR]** 。
     
     > [!NOTE]
     > 與外部資料表不同，捨棄內部資料表也會同時刪除基礎資料。
     > 
     > 

## <a id="summary"></a>摘要
如您所見，Hive 命令提供簡單的方法，以互動方式在 HDInsight 叢集上執行 Hive 查詢、監視工作狀態，以及擷取輸出。

## <a id="nextsteps"></a>接續步驟
如需 HDInsight 中 Hive 的一般資訊：

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)
* [搭配 HDInsight 上的 Hadoop 使用 MapReduce](hdinsight-use-mapreduce.md)

如果您搭配使用 Tez 和 Hive，請參閱下列文件所提供的偵錯資訊：

* [在以 Windows 為基礎的 HDInsight 上使用 Tez UI](hdinsight-debug-tez-ui.md)
* [在以 Linux 為基礎的 HDInsight 上使用 Ambari Tez 檢視](hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md



[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png


