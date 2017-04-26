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
ms.date: 04/05/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 7a1757a7ef2881b9e09389745a8e5aeaea2abf9d
ms.lasthandoff: 04/12/2017


---
# <a name="use-hive-with-hadoop-in-hdinsight-with-beeline"></a>利用 Beeline 搭配使用 Hive 與 HDInsight 中的 Hadoop
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

了解如何使用 [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) 在 HDInsight 上執行 Hive 查詢。

Beeline 是命令列工具，隨附於您的 HDInsight 叢集的前端節點。 它會使用 JDBC 連線至 HiveServer2，它是裝載在 HDInsight 叢集上的服務。 下表提供與 Beeline 搭配使用的連接字串：

| 您執行 Beeline 的來源 | Connection string | 其他參數 |
| --- | --- | --- |
| 對前端節點的 SSH 連線 | `jdbc:hive2://localhost:10001/;transportMode=http` | `-n admin` |
| 邊緣節點 | `jdbc:hive2://headnodehost:10001/;transportMode=http` | `-n admin` |
| 叢集外部 | `jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2` | `-n admin -p password` |

> [!NOTE]
> 將 'admin' 取代為叢集的叢集登入帳戶。
>
> 將 'password' 取代為叢集登入帳戶的密碼。
>
> 將 `clustername` 替換為 HDInsight 叢集的名稱。

## <a id="prereq"></a>必要條件

* HDInsight 叢集上以 Linux 為基礎的 Hadoop。

  > [!IMPORTANT]
  > Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [HDInsight 3.2 和 3.3 版取代](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date)。

* SSH 用戶端。 如需使用 SSH 的詳細資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a id="ssh"></a>使用 SSH 連線

使用下列命令透過 SSH 連線至您的叢集：

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

將 `sshuser` 取代為叢集的 SSH 帳戶。 將 `myhdinsight` 替換為 HDInsight 叢集的名稱。

**如果您提供密碼進行 SSH 驗證** (在建立 HDInsight 叢集時)，則必須在出現提示時提供密碼。

**如果您提供憑證金鑰進行 SSH 驗證** (在建立 HDInsight 叢集時)，可能需要指定用戶端系統上私密金鑰的位置：

    ssh -i ~/.ssh/mykey.key ssh@myhdinsight-ssh.azurehdinsight.net

如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a id="beeline"></a>使用 Beeline 命令

1. 在 SSH 工作階段中，使用以下命令啟動 Beeline：

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    此命令會啟動 Beeline 用戶端，並連線至叢集前端節點上的 HiveServer2。 `-n` 參數用來提供叢集登入帳戶。 預設登入是 `admin`。 如果您在叢集建立期間使用不同的名稱，請使用該名稱，而不是 `admin`。

    命令完成之後，您會看見 `jdbc:hive2://localhost:10001/>` 提示字元。

2. Beeline 命令以 `!` 字元開頭，例如 `!help` 顯示說明。 不過，一些命令可以省略 `!`。 例如，`help` 也能運作。

    會有一個 `!sql`，用來執行 HiveQL 陳述式。 不過，HiveQL 如此常用，因此您可以省略前面的 `!sql`。 下列兩個陳述式是相等的：

    ```hiveql
    !sql show tables;
    show tables;
    ```

    新的叢集上只會列出一個資料表：**hivesampletable**。

3. 使用下列命令來顯示 hivesampletable 的結構描述：

    ```bash
    describe hivesampletable;
    ```

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

    此資訊描述資料表中的資料行。 我們雖可對此資料執行某些查詢，但讓我們改為建立全新的資料表來示範如何將資料載入 Hive 及套用結構描述。

4. 輸入下列陳述式，以使用 HDInsight 叢集隨附的範例資料來建立名為 **log4jLogs** 的資料表：

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    這些陳述式會執行下列動作：

    * **DROP TABLE** - 如果資料表存在，則會刪除它。

    * **CREATE EXTERNAL TABLE** - 在 Hive 中建立「外部」資料表。 外部資料表只會將資料表定義儲存在 Hive 中。 資料會留在原來的位置。

    * **ROW FORMAT**：格式化資料的方式。 在此情況下，每個記錄中的欄位會以空格隔開。

    * **STORED AS TEXTFILE LOCATION** - 儲存資料的所在位置以及以何種檔案格式儲存。

    * **SELECT** - 選擇其資料欄 **t4** 包含值 **[ERROR]** 的所有資料列計數。 此查詢會傳回值 **3** ，因為有 3 個資料列包含此值。

    * **INPUT__FILE__NAME LIKE '%.log'** - 範例資料檔案會與其他檔案一起儲存。 此陳述式會將查詢限制在儲存在檔案結尾為 .log 中的資料。

  > [!NOTE]
  > 當您預期會由外部來源來更新基礎資料時，請使用外部資料表。 例如，自動化的資料上傳程序，或透過其他 MapReduce 作業。
  >
  > 捨棄外部資料表並 **不會** 刪除資料，只會刪除資料表定義。

    此命令的輸出類似下列文字：

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

5. 若要結束 Beeline，請使用 `!exit`。

## <a id="file"></a>執行 HiveQL 檔案

使用下列步驟建立檔案，然後利用執行該檔案。

1. 使用以下命令，建立名為 **query.hql** 的檔案：

    ```bash
    nano query.hql
    ```

2. 使用下列文字做為檔案的內容。 此查詢將建立名為 **errorLogs** 的新「內部」資料表：

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    這些陳述式會執行下列動作：

    * **CREATE TABLE IF NOT EXISTS** - 如果資料表尚不存在，則會建立它。 因為未使用 **EXTERNAL** 關鍵字，這個陳述式會建立內部資料表。 內部資料表儲存在 Hive 資料倉儲中，並完全由 Hive 管理。
    * **STORED AS ORC** - 以最佳化資料列單欄式 (Optimized Row Columnar, ORC) 格式儲存資料。 ORC 格式是高度最佳化且有效率的 Hive 資料儲存格式。
    * **INSERT OVERWRITE ...SELECT**- 從包含 **[ERROR]** 的 **log4jLogs** 資料表選取資料列，然後將資料插入 **errorLogs** 資料表。

    > [!NOTE]
    > 與外部資料表不同之處在於，捨棄內部資料表也會刪除基礎資料。

3. 若要儲存檔案，請使用 **Ctrl**+**_X**，然後輸入 **Y**，最後按 **Enter**。

4. 使用下列命令，以使用 Beeline 來執行檔案。 以稍早取得的前端節點名稱取代 **HOSTNAME**，並以系統管理員帳戶的密碼取代 **PASSWORD**：

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i query.hql
    ```

    > [!NOTE]
    > `-i` 參數會啟動 Beeline、執行 query.hql 檔案中的陳述式。 當查詢完成時，您會看到 `jdbc:hive2://localhost:10001/>` 提示字元。 您也可以使用 `-f` 參數執行檔案，它會在查詢完成後結束 Beeline。

5. 若要確認 **errorLogs** 資料表已建立，請使用下列陳述式傳回 **errorLogs** 的所有資料列：

    ```hiveql
    SELECT * from errorLogs;
    ```

    應該傳回三個資料列，且在資料行 t4 中全部包含 **[ERROR]** ：

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a name="edge-nodes"></a>邊緣節點

如果您的叢集有邊緣節點，使用 SSH 連線時，建議一律使用邊緣節點而非前端節點。 若要透過 SSH 連線對邊緣節點啟動 Beeline，請使用下列命令：

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -n admin
```

## <a name="remote-clients"></a>遠端用戶端

如果您已在本機安裝 Beeline，或是正透過 Docker 映像如 [sutoiku/beeline](https://hub.docker.com/r/sutoiku/beeline/) 使用它，您必須使用下列參數：

* __連接字串__：`-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'`

* __叢集登入名稱__：`-n admin`

* __叢集登入密碼__ `-p 'password'`

將連接字串中的 `clustername` 取代為您的 HDInsight 叢集的名稱。

將 `admin` 取代為叢集登入的名稱，以及將 `password` 取代為您的叢集登入的密碼。

## <a id="summary"></a><a id="nextsteps"></a>後續步驟

如需有關 HDInsight 中 Hive 的更多一般資訊，請參閱下列文件：

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

如需您可以使用 HDInsight 上的 Hadoop 之其他方式的詳細資訊，請參閱下列文件：

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)
* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-use-mapreduce.md)

如果您搭配使用 Tez 和 Hive，請參閱下列文件：

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

