---
title: "了解什麼是 Hive 以及如何使用 HiveQL | Microsoft Docs"
description: "了解 Apache Hive 以及如何搭配 HDInsight 中的 Hadoop 使用它。 選擇如何執行 Hive 工作，並使用 HiveQL 分析範例 Apache Log4j 檔案。"
keywords: "hiveql,什麼是 hive"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2c10f989-7636-41bf-b7f7-c4b67ec0814f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: db324cc8269a1f23613e5193f5a1dec1d26a62b1
ms.lasthandoff: 04/17/2017


---
# <a name="use-hive-and-hiveql-with-hadoop-in-hdinsight"></a>在 HDInsight 中將 Hive 和 HiveQL 與 Hadoop 搭配使用

了解如何使用 Hive 搭配 HDInsight。 使用下表了解使用 Hive 搭配 HDInsight 的各種方式︰

| **使用此方法**  | ...一個 **互動式** 殼層 | ...**批次** 處理 | ...搭配此 **叢集作業系統** | ...從此 **用戶端作業系統** |
|:--- |:---:|:---:|:--- |:--- |
| [Hive 檢視](hdinsight-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |任何 (以瀏覽器為基礎) |
| [Beeline 命令](hdinsight-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux、Unix、Mac OS X 或 Windows |
| [REST API](hdinsight-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux 或 Windows |Linux、Unix、Mac OS X 或 Windows |
| [查詢主控台](hdinsight-hadoop-use-hive-query-console.md) (HDInsight 3.2 和 3.3) |&nbsp; |✔ |Windows |任何 (以瀏覽器為基礎) |
| [HDInsight Tools for Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) |&nbsp; |✔ |Linux 或 Windows |Windows |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux 或 Windows |Windows |
| [遠端桌面](hdinsight-hadoop-use-hive-remote-desktop.md) (HDInsight 3.2 和 3.3) |✔ |✔ |Windows |Windows |

> [!IMPORTANT]
> Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [HDInsight 3.2 和 3.3 取代](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date)。

## <a id="why"></a>什麼是 Hive

[Apache Hive](http://hive.apache.org/) 是適用於 Hadoop 的資料倉儲系統，可執行資料摘要、查詢以及資料分析。 Hive 可讓您將結構投影在大量非結構化資料上。 定義結構後，您不須具備 Jave 或 MapReduce 相關知識，即可使用 Hive 查詢該資料。

Hive 查詢是以 HiveQL 撰寫而成，這是類似 SQL 的查詢語言。 Hive 可以互動方式用於探索資料，或者建立可重複使用的批次處理工作。

Hive 了解如何處理結構化和半結構化資料 (例如，以特定字元分隔欄位的文字檔)。 Hive 也支援自訂複雜或不規則結構化資料的 **序列化/反序列化程式 (SerDe)** 。 如需詳細資訊，請參閱[如何搭配 HDInsight 使用自訂 JSON SerDe](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx) 文件 (英文)。

## <a name="user-defined-functions-udf"></a>使用者定義函數 (UDF)

Hive 也可透過 **使用者定義函數 (UDF)**延伸。 UDF 可讓您在 HiveQL 中實作功能或不易模型化的邏輯。 如需將 UDF 與 Hive 搭配使用的範例，請參閱以下文件：

* [將 Java 使用者定義函式與 Hive 搭配使用](hdinsight-hadoop-hive-java-udf.md)

* [在 HDInsight 中搭配 Hive 與 Pig 使用 Python](hdinsight-python.md)

* [在 HDInsight 中搭配 Hive 與 Pig 使用 C#](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [如何將自訂 Hive UDF 新增至 HDInsight (英文)](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [自訂 Hive UDF 範例以將日期/時間格式轉換為 Hive 時間戳記](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="hive-internal-tables-vs-external-tables"></a>Hive 內部和外部資料表比較。

關於 Hive 內部資料表和外部資料表，有若干事項您必須了解：

* **CREATE TABLE** 命令會建立內部資料表。 資料檔案必須位於預設容器中。

* **CREATE TABLE** 命令會將資料檔案移至叢集預設儲存體的 `/hive/warehouse/<TableName>` 目錄。

* **CREATE EXTERNAL TABLE** 命令會建立外部資料表。 資料檔案可位於預設容器外。

* **CREATE EXTERNAL TABLE** 命令不會移動資料檔案。

如需詳細資訊，請參閱 [HDInsight：Hive 內部和外部資料表簡介][cindygross-hive-tables]。

## <a id="data"></a>範例資料

HDInsight 提供各種範例資料集，這些範例資料及儲存在 `/example/data` 和 `/HdiSamples` 目錄。 這些目錄存在於您叢集的預設儲存體中。 本文件使用 `/example/data/sample.log` 檔案。 此檔案為 *log4j* 檔案。 檔案中的每一筆記錄均由一列欄位組成，包括以 `[LOG LEVEL]` 欄位來顯示類型和嚴重性，例如：

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

在上一個範例中，記錄層級是「錯誤」。

> [!NOTE]
> 您也可以使用 [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) 記錄工具產生 log4j 檔案，然後將該檔案上傳至 Blob 容器。 如需指示，請參閱 [將資料上傳至 HDInsight](hdinsight-upload-data.md) 。 如需關於如何搭配 HDInsight 使用 Azure Blob 儲存體的詳細資訊，請參閱 [搭配 HDInsight 使用 Azure Blob 儲存體](hdinsight-hadoop-use-blob-storage.md)。

## <a id="job"></a>範例 Hive 查詢

下列 HiveQL 陳述式將資料行投影在 `/example/data/sample.log` 檔案︰

    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

在上一個範例中，HiveQL 陳述式會執行下列動作：

* **set hive.execution.engine=tez;**：設定執行引擎以使用 Tez。 使用 Tez 而非 MapReduce，可以提升查詢效能。 如需 Tez 的詳細資訊，請參閱 [使用 Apache Tez 以提升效能](#usetez) 一節。

    > [!NOTE]
    > 只有在使用以 Windows 為基礎的 HDInsight 叢集時，才需要此陳述式。 Tez 是以 Linux 為基礎的 HDInsight 預設的執行引擎。

* **DROP TABLE**︰如果資料表已存在，請刪除資料表。

* **CREATE EXTERNAL TABLE**：在 Hive 中建立新的**外部**資料表。 外部資料表只會將資料表定義儲存在 Hive 中。 資料會留在原來的位置，並保持原始格式。

* **ROW FORMAT**：告訴 Hive 如何格式化資料。 在此情況下，每個記錄中的欄位會以空格隔開。

* **STORED AS TEXTFILE LOCATION**：將資料的儲存位置告訴 Hive (`example/data` 目錄)，且資料儲存為文字 資料可以在目錄的一個檔案中，也可以分散在多個檔案中。

* **SELECT**：選取資料行 **t4** 包含 **[ERROR]** 值的所有資料列計數。 這個陳述式會傳回值 **3**，因為有三個資料列包含此值。

* **INPUT__FILE__NAME LIKE '%.log'** - 告訴 Hive 我們只應該從檔名以 log 結尾的檔案中傳回資料。 這個陳述式會將搜尋限制為包含資料的 `sample.log` 檔案。

> [!NOTE]
> 當您預期會由外部來源來更新基礎資料時，請使用外部資料表。 例如，自動化的資料上傳程序，或 MapReduce 作業。
>
> 捨棄外部資料表並「不會」  刪除資料，只會刪除資料表定義。

若要建立**內部**資料表，而不是外部資料表，請使用下列 HiveQL：

    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs
    SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

這些陳述式會執行下列動作：

* **CREATE TABLE IF NOT EXISTS**︰如果資料表不存在，請建立資料表。 因為未使用 **EXTERNAL** 關鍵字，所以這個陳述式會建立內部資料表，而內部資料表儲存在 Hive 資料倉儲中，並完全透過 Hive 來管理。

* **STORED AS ORC**：以最佳化資料列單欄式 (ORC) 格式儲存資料。 這是高度最佳化且有效率的 Hive 資料儲存格式。

* **INSERT OVERWRITE ...SELECT**：從 **log4jLogs** 資料表選取包含 **[ERROR]** 的資料列，然後將資料插入 **errorLogs** 資料表。

> [!NOTE]
> 與外部資料表不同之處在於，捨棄內部資料表也會刪除基礎資料。

## <a id="usetez"></a>使用 Apache Tez 以提升效能

[Apache Tez](http://tez.apache.org) 是可讓資料高用量應用程式 (例如 Hive)，以大規模而更有效率方式執行作業的架構。 在最新版的 HDInsight 中，Hive 已支援在 Tez 上執行。 對於以 Linux 為基礎的 HDInsight 叢集，Tez 預設為開啟。

> [!NOTE]
> 對於 Windows 型的 HDInsight 叢集，Tez 目前預設為關閉，因而必須啟用。 若要充分發揮 Tez 的效益，您必須設定 Hive 查詢的下列值：
>
> `set hive.execution.engine=tez;`
>
> 對於以 Linux 為基礎的 HDInsight 叢集，Tez 是預設引擎。

[Tez 上的 Hive 設計文件](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez)包含實作選擇和調整組態的詳細資料。

為了使用 Tez 來協助偵錯作業，HDInsight 提供下列 Web UI，讓您檢視 Tez 作業的詳細資料：

* [在以 Linux 為基礎的 HDInsight 上使用 Ambari Tez 檢視](hdinsight-debug-ambari-tez-view.md)

* [在以 Windows 為基礎的 HDInsight 上使用 Tez UI](hdinsight-debug-tez-ui.md)

## <a id="run"></a>如何執行 HiveQL 工作

HDInsight 可以使用各種方法執行 Hive QL 工作。 請使用下表決定適合您的方法，然後跟著連結逐項閱讀介紹。

| **使用此方法**  | ...一個 **互動式** 殼層 | ...**批次** 處理 | ...搭配此 **叢集作業系統** | ...從此 **用戶端作業系統** |
|:--- |:---:|:---:|:--- |:--- |
| [Hive 檢視](hdinsight-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |任何 (以瀏覽器為基礎) |
| [Beeline 命令](hdinsight-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux、Unix、Mac OS X 或 Windows |
| [REST API](hdinsight-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux 或 Windows |Linux、Unix、Mac OS X 或 Windows |
| [查詢主控台](hdinsight-hadoop-use-hive-query-console.md) (HDInsight 3.2 和 3.3) |&nbsp; |✔ |Windows |任何 (以瀏覽器為基礎) |
| [HDInsight Tools for Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) |&nbsp; |✔ |Linux 或 Windows |Windows |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux 或 Windows |Windows |
| [遠端桌面](hdinsight-hadoop-use-hive-remote-desktop.md) (HDInsight 3.2 和 3.3) |✔ |✔ |Windows |Windows |

> [!IMPORTANT]
> Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [HDInsight 3.2 和 3.3 取代](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date)。

## <a name="hive-jobs-and-sql-server-integration-services"></a>Hive 工作和 SQL Server 整合服務

您也可以使用 SQL Server Integration Services (SSIS) 來執行 Hive 工作。 適用於 SSIS 的 Azure Feature Pack 中提供下列元件可搭配 HDInsight 上的 Hive 工作使用。

* [Azure HDInsight Hive 工作][hivetask]

* [Azure 訂用帳戶連接管理員][connectionmanager]

在[這裡][ssispack]深入了解適用於 SSIS 的 Azure Feature Pack。

## <a id="nextsteps"></a>接續步驟

現在您已了解什麼是 Hive 以及如何搭配 HDInsight 中的 Hadoop 使用它，接著請使用下列連結探索 Azure HDInsight 的其他使用方式。

* [將資料上傳至 HDInsight][hdinsight-upload-data]
* [搭配 HDInsight 使用 Pig][hdinsight-use-pig]
* [搭配 HDInsight 使用 MapReduce 作業][hdinsight-use-mapreduce]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

