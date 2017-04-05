---
title: "使用 Beeline 與 HDInsight (Hadoop) 上的 Hive 搭配作業 | Microsoft Docs"
description: "學習如何使用 SSH 連線至 HDInsight 中的 Hadoop 叢集，然後使用 Beeline 以互動方式提交 Hive 查詢。 Beeline 是透過 JDBC 與 HiveServer2 搭配作業的公用程式。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 3adfb1ba-8924-4a13-98db-10a67ab24fca
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
ms.openlocfilehash: b015e3d99c72c1840a31bacee5d9655fa9f48da7
ms.lasthandoff: 03/25/2017


---
# <a name="use-hive-with-hadoop-in-hdinsight-with-beeline"></a>利用 Beeline 搭配使用 Hive 與 HDInsight 中的 Hadoop
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

在本文中，您將學習如何使用安全殼層 (SSH) 連接至以 Linux 為基礎的 HDInsight 叢集，然後使用 [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) 命令列工具以互動方式提交 Hive 查詢。

> [!NOTE]
> Beeline 使用 JDBC 連接到 Hive。 如需搭配 Hive 使用 JDBC 的詳細資訊，請參閱 [使用 Hive JDBC 驅動程式連接到 Azure HDInsight 上的 Hive](hdinsight-connect-hive-jdbc-driver.md)。

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

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**如果您提供密碼進行 SSH 驗證** (在建立 HDInsight 叢集時)，則需要在出現提示時提供密碼。

如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a id="beeline"></a>使用 Beeline 命令
1. 連線之後，使用下列命令啟動 Beeline：
   
        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
   
    這將會啟動 Beeline 用戶端及連接 JDBC URL。 在這裡我們使用 `localhost` ，因為 HiveServer2 會在叢集中的兩個前端節點上執行，而且我們要在主要前端節點上直接執行 Beeline。
   
    命令完成之後，您將會看見 `jdbc:hive2://localhost:10001/>` 提示字元。
2. Beeline 命令通常以 `!` 字元開頭，例如 `!help` 顯示說明。 不過，往往會省略 `!` 。 例如， `help` 也可行。
   
    如果您檢視說明，您會注意到用來執行 HiveQL 陳述式的 `!sql`。 不過，HiveQL 如此常用，因此您可以省略前面的 `!sql`。 下列兩個陳述式的結果完全相同；顯示目前可透過 Hive 取得的資料表：
   
        !sql show tables;
        show tables;
   
    新的叢集上只能列出一個資料表︰ **hivesampletable**。
3. 使用下列命令來顯示 hivesampletable 的結構描述︰
   
        describe hivesampletable;
   
    此命令會傳回下列資訊：
   
        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+
   
    這會顯示資料表中的資料行。 我們雖可對此資料執行某些查詢，但讓我們改為建立全新的資料表來示範如何將資料載入 Hive 及套用結構描述。
4. 輸入下列陳述式，以使用 HDInsight 叢集隨附的範例資料來建立名為 **log4jLogs** 的新資料表：
   
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
   * **INPUT__FILE__NAME LIKE '%.log'** - 告訴 Hive 我們只應該從檔名以 log 結尾的檔案中傳回資料。 通常在使用 hive 查詢時，您在相同的資料夾中只會有具有相同結構描述的資料，不過此範例記錄檔會以其他資料格式儲存。
     
     > [!NOTE]
     > 當您預期以外部來源更新基礎資料 (例如自動化資料上傳程序)，或以其他 MapReduce 作業更新基礎資料，但希望 Hive 查詢一律使用最新資料時，必須使用外部資料表。
     > 
     > 捨棄外部資料表並 **不** 會刪除資料，只會刪除資料表定義。
     > 
     > 
     
     此命令的輸出應類似這樣：

     ```
     INFO  : Tez session hasn't been created yet. Opening session
     INFO  :
     
     INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)
     
     INFO  : Map 1: -/-      Reducer 2: 0/1
     INFO  : Map 1: 0/1      Reducer 2: 0/1
     INFO  : Map 1: 0/1      Reducer 2: 0/1
     INFO  : Map 1: 0/1      Reducer 2: 0/1
     INFO  : Map 1: 0/1      Reducer 2: 0/1
     INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
     INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
     INFO  : Map 1: 1/1      Reducer 2: 0/1
     INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
     INFO  : Map 1: 1/1      Reducer 2: 1/1
     +----------+--------+--+
     |   sev    | count  |
     +----------+--------+--+
     | [ERROR]  | 3      |
     +----------+--------+--+
     1 row selected (47.351 seconds)
     ```
5. 若要結束 Beeline，請使用 `!quit`。

## <a id="file"></a>執行 HiveQL 檔案
Beeline 也可以用來執行包含 HiveQL 陳述式的檔案。 使用下列步驟建立檔案，然後利用執行該檔案。

1. 使用以下命令，建立名為 **query.hql**的新檔案：
   
        nano query.hql
2. 編輯器開啟時，請使用下列做為檔案的內容。 此查詢將建立名為 **errorLogs**的新「內部」資料表：
   
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   
    這些陳述式會執行下列動作：
   
   * **CREATE TABLE IF NOT EXISTS** - 建立資料表 (如果不存在)。 因為未使用 **EXTERNAL** 關鍵字，所以這是內部資料表，而內部資料表儲存在 Hive 資料倉儲中，並完全透過 Hive 所管理。
   * **STORED AS ORC** - 以最佳化資料列單欄式 (Optimized Row Columnar, ORC) 格式儲存資料。 這是高度最佳化且有效率的 Hive 資料儲存格式。
   * **INSERT OVERWRITE ...SELECT**- 從包含 **[ERROR]** 的 **log4jLogs** 資料表選取資料列，然後將資料插入 **errorLogs** 資料表。
     
     > [!NOTE]
     > 與外部資料表不同，捨棄內部資料表也會同時刪除基礎資料。
     > 
     > 
3. 若要儲存檔案，請使用 **Ctrl**+**_X**，然後輸入 **Y**，最後按 **Enter**。
4. 使用下列命令，以使用 Beeline 來執行檔案。 以稍早取得的前端節點名稱取代 **HOSTNAME**，並以系統管理員帳戶的密碼取代 **PASSWORD**：
   
        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i query.hql
   
   > [!NOTE]
   > `-i` 參數會啟動 Beeline、執行 query.hql 檔案中的陳述式，並停留在 Beeline 的 `jdbc:hive2://localhost:10001/>` 提示字元中。 您也可以使用 `-f` 參數執行檔案，它會在檔案處理完畢後讓您返回 Bash。
   > 
   > 
5. 若要確認 **errorLogs** 資料表已建立，請使用下列陳述式傳回 **errorLogs** 的所有資料列：
   
        SELECT * from errorLogs;
   
    應該傳回三個資料列，且在資料行 t4 中全部包含 **[ERROR]** ：
   
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a name="more-about-beeline-connectivity"></a>進一步了解 Beeline 連線
此文件中的步驟使用 `localhost` 來連接到叢集前端節點上執行的 HiveServer2。 您也可以使用需要處理額外步驟的主機名稱或前端節點完整網域名稱 (尋找主機名稱或 FQDN 的步驟)。 從前端節點使用 Beeline 時，使用 `localhost` 已足夠。

如果您在叢集中有邊緣節點，安裝 Beeline 時，您必須使用主機名稱或前端節點的 FQDN 來連接。

如果您有 Beeline 安裝在您的叢集外的用戶端上，您可以使用下列命令連接。 將 **CLUSTERNAME** 取代為 HDInsight 叢集的名稱。 將 **PASSWORD** 取代為管理員 (HTTP 登入) 帳戶的密碼。

    beeline -u 'jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=hive2' -n admin -p PASSWORD

請注意，參數/URI 與直接在前端節點上執行，或從叢集中的邊緣節點執行時不同。 這是因為從網際網路連接到叢集會使用公用閘道器，透過連接埠 443 路由流量。 此外，數個其他服務是透過連接埠 443 上的公用閘道器公開，因此 URI 與直接連接時不同。 從網際網路連接時您也必須提供密碼來驗證工作階段。

## <a id="summary"></a><a id="nextsteps"></a>後續步驟
如您所見，Beeline 命令提供簡單的方法，以互動方式在 HDInsight 叢集上執行 Hive 查詢。

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


