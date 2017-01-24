---
title: "什麼是 Hadoop？ Azure HDInsight 簡介 | Microsoft Docs"
description: "介紹 HDInsight 中 Hadoop 生態系統與元件。 HDInsight 包括 Hadoop、Spark、HBase 和巨量資料處理和分析的更多資訊。"
keywords: "巨量資料分析、Hadoop 簡介、Hadoop 是什麼、Hadoop 技術堆疊、Hadoop 生態系統"
services: hdinsight
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: e56a396a-1b39-43e0-b543-f2dee5b8dd3a
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/14/2016
ms.author: cgronlun
translationtype: Human Translation
ms.sourcegitcommit: 10d684bd6c9408f2fbd48a0a8804b011f098d71c
ms.openlocfilehash: 8f38b9eaf8bcb498a7983756cbf57f3e372d7fe0


---
# <a name="an-introduction-to-the-hadoop-ecosystem-on-azure-hdinsight"></a>Azure HDInsight 上的 Hadoop 生態系統簡介
 本文提供 Azure HDInsight 上的 Hadoop、其生態系統和巨量資料的簡介。 深入了解 Hadoop 元件、常見術語以及巨量資料分析的案例。

## <a name="what-is-hadoop-on-hdinsight"></a>HDInsight 上的 Hadoop 是什麼？
Hadoop 是指開放原始碼軟體的生態系統，這是分散式處理、儲存的和分析電腦叢集上巨量資料集的架構。 Azure HDInsight 可讓 **Hortonworks 資料平台 (HDP)** 散發中的 Hadoop 元件在雲端取得，還可部署具高可靠性和可用性的受管理叢集，並提供具 Active Directory 的企業級安全性與管理。  

Apache Hadoop 是可用於巨量資料處理的原始開放原始碼專案。 以下是屬於 Hadoop 技術堆疊之相關軟體和公用程式的開發，包括 Apache Hive、Apache HBase、Apache Spark、Apache Kafka 及其他許多項目。 如需詳細資訊，請參閱 [HDInsight 中的 Hadoop 生態系統概觀](#overview)。

## <a name="what-is-big-data"></a>什麼是巨量資料？
巨量資料是指任何極為龐大的數位資訊，包括 Twitter 摘要中的文字、來自工業設備的感應器資訊，以及客戶在網站上瀏覽和購物的相關資訊。 巨量資料可以屬於過去 (亦即儲存的資料) 或即時 (亦即直接從來源串流而來)。 巨量資料的收集量快速增加，收集速度愈來愈快，收集格式也愈來愈多。

對於要提供可採取行動之情報或深入解析的巨量資料，您必須收集相關資料並提出適當的問題。 您也必須確定資料可存取、已清除、已分析，然後以實用的方式呈現。 這就是 HDInsight 中的 Hadoop 上巨量資料的用途。

## <a name="a-nameoverviewaoverview-of-the-hadoop-ecosystem-in-hdinsight"></a><a name="overview"></a>HDInsight 中的 Hadoop 生態系統概觀
HDInsight 是在 Microsoft Azure 上散發的雲端服務，可快速擴充 Apache Hadoop 技術堆疊以便進行巨量資料分析。 其包含 Apache Spark、HBase、Kafka、Storm、Pig、Hive、互動式 Hive、Sqoop、Oozie、Ambari 等實作。 HDInsight 同時結合了商業智慧 (BI) 工具，例如 Excel、SQL Server Analysis Services 和 SQL Server Reporting Services。

### <a name="hadoop-hbase-spark-kafka-interactive-hive-storm-customized-and-other-clusters"></a>Hadoop、HBase、Spark、Kafka、 互動式 Hive、Storm、自訂和其他叢集
HDInsight 提供下列叢集類型：

* **[Apache Hadoop](https://wiki.apache.org/hadoop)**：使用 [HDFS](#hdfs) 提供可靠的資料儲存，以及提供簡單的 [MapReduce](#mapreduce) 程式設計模型以平行處理和分析資料。
* **[Apache Spark](http://spark.apache.org/)**：一種平行處理架構，可支援記憶體內的處理，大幅提升巨量資料分析應用程式、SQL 之 Spark 工作、串流資料與機器學習的效能。 請參閱 [概觀：HDInsight 的 Apache Spark 是什麼？](hdinsight-apache-spark-overview.md)
* **[Apache HBase](http://hbase.apache.org/)**：建置於 Hadoop 上的 NoSQL 資料庫，可針對大量非結構化及半結構化資料 (可能是數十億的資料列乘以數百萬的資料行)，提供隨機存取功能和強大一致性。 請參閱＜ [HDInsight 上的 HBase 概觀](hdinsight-hbase-overview.md)＞。
* **[Microsoft R 伺服器](https://msdn.microsoft.com/en-us/microsoft-r/rserver)**︰可用來裝載和管理並行、分散式 R 程序的企業級伺服器。 這項新功能可讓資料科學家、統計學家以及 R 程式設計人員隨其所需存取 HDInsight 上可調整大小的分散式分析方法。 請參閱 [HDInsight 中的 R 伺服器概觀](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-r-server-overview)。
* **[Apache Storm](https://storm.incubator.apache.org/)**：分散式、即時的運算系統，可快速處理大型的資料串流。 Storm 以受管理叢集的形式在 HDInsight 中提供。 請參閱＜ [使用 Storm 和 Hadoop 來分析即時感應器資料](hdinsight-storm-sensor-data-analysis.md)＞。
* **[Apache 互動式 Hive 預覽 (也稱為︰Live Long and Process)](https://cwiki.apache.org/confluence/display/Hive/LLAP)**︰更快速之互動式 Hive 查詢的記憶體內快取。 請參閱[在 HDInsight 中使用互動式 Hive](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-use-interactive-hive)。
* **[Apache Kafka 預覽](https://kafka.apache.org/)**︰用來建立串流資料管線和應用程式的開放原始碼平台。 Kafka 也提供訊息佇列功能，可讓您發佈和訂閱資料串流。 請參閱 [HDInsight 上的 Apache Kafka 簡介](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-kafka-introduction)。
* **[已加入網域的叢集預覽](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-domain-joined-introduction)**︰Active Directory 網域中加入的叢集，如此您可以控制存取並提供對資料的管理。
* **[具指令碼動作的自訂叢集](hdinsight-hadoop-customize-cluster-linux.md)**：具指令碼的叢集，該指令碼於佈建期間執行，並安裝其他元件。

### <a name="example-customization-scripts"></a>範例自訂指令碼
指令碼動作是在叢集佈建期間在 Linux 上執行的 Bash 指令碼，而且可用來在叢集上安裝其他元件。

下列指令碼範例由 HDInsight 小組提供：

* [色調](hdinsight-hadoop-hue-linux.md)：一組 Web 應用程式，可用來與叢集互動。 僅限於 Linux 叢集。
* [Giraph](hdinsight-hadoop-giraph-install-linux.md)：物品與人員間之模型關聯性的圖形處理。
* [R](hdinsight-hadoop-r-scripts-linux.md)：一種開放原始碼語言與環境，可在機器學習中用於統計計算。
* [Solr](hdinsight-hadoop-solr-install-linux.md)：一種企業級搜尋平台，可對資料進行全文檢索搜尋。

如需開發您自己的指令碼動作相關資訊，請參閱 [使用 HDInsight 開發指令碼動作](hdinsight-hadoop-script-actions-linux.md)。

## <a name="what-are-the-hadoop-components-and-utilities"></a>Hadoop 元件與公用程式是什麼？
HDInsight 叢集包含下列元件及公用程式。

* **[Ambari](#ambari)**：叢集佈建、管理、監視和公用程式。
* **[Avro](#avro)** (Microsoft .NET Library for Avro)：Microsoft.NET 環境的資料序列化。
* **[Hive & HCatalog](#hive)**：類似結構化查詢語言 (SQL) 的查詢，以及資料表和儲存體管理層。
* **[Mahout](#mahout)**：適用於可調整的機器學習應用程式。
* **[MapReduce](#mapreduce)**：Hadoop 分散式處理和資源管理的傳統架構。 請參閱新一代的資源架構 [YARN](#yarn)。
* **[Oozie](#oozie)**：工作流程管理。
* **[Phoenix](#phoenix)**：對 HBase 的關聯式資料庫層。
* **[Pig](#pig)**：簡單撰寫 MapReduce 轉換的指令碼。
* **[Sqoop](#sqoop)**：資料匯入和匯出。
* **[Tez](#tez)**：允許資料密集程序有效率地大規模執行。
* **[YARN](#yarn)**：Hadoop 核心程式庫的一部分，並且是新一代的 MapReduce 軟體架構。
* **[ZooKeeper](#zookeeper)**：協調分散式系統中的程序。

> [!NOTE]
> 如需特定元件及版本的相關資訊，請參閱 [Hadoop 元件、版本設定和 HDInsight 的服務供應項目][component-versioning]
>
>

### <a name="a-nameambariaambari"></a><a name="ambari"></a>Ambari
Apache Ambari 可用來佈建、管理及監視 Apache Hadoop 叢集。 其中包含一組直接易懂的操作員工具和健全的 API 集，可消除 Hadoop 的複雜性，並簡化叢集作業。 在 Linux 上的 HDInsight 叢集同時提供 Ambari Web UI 和 Ambari REST API，而在 Windows 上的叢集提供 REST API 的子集。 HDInsight 叢集上的 Ambari 檢視允許外掛程式 UI 功能。

請參閱 [使用 Ambari 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md) (僅限 Linux)、[使用 Ambari API 監視 HDInsight 上的 Hadoop 叢集](hdinsight-monitor-use-ambari-api.md)及 <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Apache Ambari API 參考資料</a>。

### <a name="a-nameavroaavro-microsoft-net-library-for-avro"></a><a name="avro"></a>Avro (Microsoft .NET Library for Avro)
Microsoft .NET Library for Avro 針對 Microsoft .NET 環境的序列化，實作 Apache Avro 壓縮二進位資料交換格式。 它採用 <a target="_blank" href="http://www.json.org/">JavaScript 物件標記法 (JSON)</a> 來定義不限語言的結構描述，支援語言互通性，這表示以某種語言序列化的資料可供另一種語言讀取。 如需此格式的詳細資訊，請參閱 <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Apache Avro 規格</a>。
Avro 檔案格式支援分散式 MapReduce 程式設計模型。 檔案「可分割」，這表示您可以從檔案中的任何一點開始讀取特定區塊。 如需了解作法，請參閱＜ [使用 Microsoft .NET Library for Avro 將資料序列化](hdinsight-dotnet-avro-serialization.md)＞。

### <a name="a-namehdfsahdfs"></a><a name="hdfs"></a>HDFS
Hadoop Distributed File System (HDFS) 是分散式檔案系統，加上 MapReduce 和 YARN，成為 Hadoop 生態系統的核心。 HDFS 是 HDInsight 上的 Hadoop 叢集的標準檔案系統。

### <a name="a-namehiveahive--hcatalog"></a><a name="hive"></a>Hive 和 HCatalog
<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> 是以 Hadoop 為基礎的資料倉儲軟體，可讓您使用一種稱為 HiveQL 的 SQL 式語言，以查詢和管理分散式儲存體中的大型資料集。 Hive 和 Pig 一樣，是以 MapReduce 為基礎的抽象。 Hive 會在執行時將查詢轉換為一系列的 MapReduce 工作。 Hive 在概念上比 Pig 更接近關聯式資料庫管理系統，因此，適用於更具結構化的資料。 對於非結構化資料，Pig 是較好的選擇。 請參閱 [在 HDInsight 上將 Hive 與 Hadoop 搭配使用](hdinsight-use-hive.md)。

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> 是 Hadoop 的資料表和儲存體管理層，可為您提供資料的關聯式檢視。 在 HCatalog 中，您可以讀寫可撰寫 Hive SerDe (序列化程式-還原序列化程式) 之任何格式的檔案。

### <a name="a-namemahoutamahout"></a><a name="mahout"></a>Mahout
<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> 是 Hadoop 上執行的機器學習演算法的可擴充程式庫。 機器學習應用程式採用統計學的原理，教導系統從資料中學習，根據過去的結果來研判未來的行為。 請參閱＜ [在 Hadoop 上使用 Mahout 來產生電影推薦](hdinsight-mahout.md)＞。

### <a name="a-namemapreduceamapreduce"></a><a name="mapreduce"></a>MapReduce
MapReduce 是供 Hadoop 撰寫應用程式來以平行方式批次處理巨量資料集的傳統軟體架構。 MapReduce 工作可分割大型資料集，並將資料組織成機碼值組以便於處理。

[YARN](#yarn) 則稱為 MapReduce 2.0，是 Hadoop 新一代的資源管理員和應用程式架構。 MapReduce 作業會在 YARN 上執行。

如需 MapReduce 的詳細資訊，請參閱 Hadoop Wiki 上的 <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> 。

### <a name="a-nameoozieaoozie"></a><a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> 是可管理 Hadoop 工作的工作流程協調系統。 它可與 Hadoop 堆疊相整合，並支援 MapReduce、Pig、Hive 和 Sqoop 的 Hadoop 工作。 它也可用來排程系統的特定工作，例如 Java 程式或 Shell 指令碼。 請參閱 [搭配使用 Oozie 與 Hadoop](hdinsight-use-oozie.md)。

### <a name="a-namephoenixaphoenix"></a><a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> 是對 HBase 的關聯式資料庫層。 Phoenix 包含 JDBC 驅動程式，可讓您直接查詢和管理 SQL 資料表。 Phoenix 會將查詢和其他陳述式轉譯為原生 NoSQL API 呼叫，而不是使用 MapReduce。因此能更快速地在 NoSQL 存放區上套用。 請參閱[搭配 HBase 叢集使用 Phoenix 和 SQuirreL](hdinsight-hbase-phoenix-squirrel.md)。

### <a name="a-namepigapig"></a><a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> 是一個高階平台，可讓您在非常大的資料集上，使用稱為 Pig Latin 的簡單指令碼語言來執行複雜的 MapReduce 轉換。 Pig 會將 Pig Latin 指令碼轉換成可在 Hadoop 內執行。 您可以建立使用者定義的函式 (UDF) 來延伸 Pig Latin。 請參閱 [搭配使用 Pig 與 Hadoop](hdinsight-use-pig.md)。

### <a name="a-namesqoopasqoop"></a><a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> 是在 Hadoop 與 SQL 之類的關聯式資料庫或其他結構化資料儲存之間盡可能以最高效率傳輸大量資料的工具。 請參閱＜ [使用 Sqoop 與 Hadoop](hdinsight-use-sqoop.md)＞。

### <a name="a-nametezatez"></a><a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> 是以 Hadoop YARN 為建置基礎的應用程式架構，可執行一般資料處理的複雜非循環圖。 它是更有彈性且功能更強大的 MapReduce 架構後繼版本，可讓資料密集程序 (例如 Hive) 更有效率地大規模執行。 請參閱 [＜使用 Hive 和 HiveQL＞中的＜使用 Apache Tez 以提升效能＞](hdinsight-use-hive.md#usetez)。

### <a name="a-nameyarnayarn"></a><a name="yarn"></a>YARN
Apache YARN 是新一代的 MapReduce (MapReduce 2.0，簡稱 MRv2)，可支援比 MapReduce 批次處理還要多的資料處理案例，並具有更佳的延展性和即時處理能力。 YARN 能提供資源管理和分散式應用程式架構。 MapReduce 作業會在 YARN 上執行。

如需了解 YARN，請參閱 <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>。

### <a name="a-namezookeeperazookeeper"></a><a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> 利用資料暫存器 (znode) 的共用階層式命名空間，協調大型分散式系統中的程序。 Znodes 包含協調程序所需的少量中繼資訊：狀態、位置、組態等。

## <a name="programming-languages-on-hdinsight"></a>HDInsight 上的程式設計語言
HDInsight 叢集 (Hadoop、HBase、Storm 和 Spark 叢集) 支援許多種程式設計語言，但某些語言並未預設安裝。 針對未預設安裝的程式庫、模組或封裝，請使用指令碼動作來安裝元件。 請參閱 [使用 HDInsight 開發指令碼動作](hdinsight-hadoop-script-actions-linux.md)。

### <a name="default-programming-language-support"></a>預設的程式設計語言支援
根據預設，HDInsight 叢集可支援：

* Java
* Python

其他語言則可以使用指令碼動作來加以安裝： [使用 HDInsight 開發指令碼動作](hdinsight-hadoop-script-actions-linux.md)。

### <a name="java-virtual-machine-jvm-languages"></a>Java 虛擬機器 (JVM) 語言
使用 Java 虛擬機器 (JVM) 即可執行 Java 以外的許多語言，不過，若要執行其中的某些語言，您可能必須在叢集上安裝其他元件。

HDInsight 叢集上支援下列以 JVM 為基礎的語言：

* Clojure
* Jython (適用於 Java 的 Python)
* Scala

### <a name="hadoop-specific-languages"></a>Hadoop 專屬語言
HDInsight 叢集支援下列 Hadoop 生態系統專屬語言：

* 適用於 Pig 工作的 Pig Latin
* 適用於 Hive 工作和 SparkSQL 的 HiveQL

## <a name="a-nameadvantageaadvantages-of-hadoop-in-the-cloud"></a><a name="advantage"></a>Hadoop 在雲端中的優點
在 Azure 雲端生態系統內，HDInsight 中的 Hadoop 提供許多優點，包括：

* 自動佈建 Hadoop 叢集。 HDInsight 叢集比手動設定 Hadoop 叢集更容易建立。 如需詳細資訊，請參閱 [在 HDInsight 中佈建 Hadoop 叢集](hdinsight-hadoop-provision-linux-clusters.md)。
* 最先進的 Hadoop 元件。 如需詳細資訊，請參閱 [Hadoop 元件、版本設定和 HDInsight 的服務供應項目][component-versioning]。
* 叢集的高可用性和可靠性。 如需詳細資訊，請參閱＜ [HDInsight 上 Hadoop 叢集的可用性和可靠性](hdinsight-high-availability-linux.md) ＞。
* 有效率又符合經濟效益的資料存放區，採用 Azure Blob 儲存體或是 Azure Data Lake Store，兩者都是與 Hadoop 相容的存放區選項。 請參閱[在 HDInsight 中搭配使用 Azure Blob 儲存體與 Hadoop](hdinsight-hadoop-use-blob-storage.md) 或[搭配使用 Data Lake Store 與 HDInsight 叢集](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)，以了解詳細資訊。
* 與其他 Azure 服務整合，包括 [Web 應用程式](https://azure.microsoft.com/documentation/services/app-service/web/)和 [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)。
* 執行 HDInsight 叢集的其他 VM 大小和類型。 如需詳細資訊，請參閱 [Hadoop 元件、版本設定和 HDInsight 的服務供應項目][component-versioning]。
* 叢集調整。 叢集調整可讓您變更執行中 HDInsight 叢集的節點數目，而不必刪除或重建它。
* 虛擬網路支援。 HDInsight 叢集可以與 Azure 虛擬網路一起使用來支援隔離雲端資源，或支援將這些雲端資源與資料中心裡的那些雲端資源連結的混合式案例。
* 進入成本低。 開始[免費試用](https://azure.microsoft.com/free/)，或請參閱[HDInsight 價格詳細資料](https://azure.microsoft.com/pricing/details/hdinsight/)。

如需深入了解 HDInsight 中的 Hadoop 優點，請參閱 [HDInsight 的 Azure 功能頁面][marketing-page]。

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard 和 HDInsight Premium
HDInsight 提供兩種類型的巨量資料雲端提供項目：Standard 和 Premium。 HDInsight Standard 提供組織可用來執行其巨量資料工作負載的企業規模叢集。 HDInsight Premium 以其為基礎，並提供 HDInsight 叢集的進階分析與安全性功能。 如需詳細資訊，請參閱 [Azure HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)

## <a name="a-idresourcesaresources-for-learning-more-about-big-data-analysis-hadoop-and-hdinsight"></a><a id="resources"></a>深入了解巨量資料分析、Hadoop 和 HDInsight 的實用資源
得利於將 Hadoop 引進雲端中，以致能夠分析下列資源的巨量資料。

### <a name="hadoop-documentation-for-hdinsight"></a>HDInsight 的 Hadoop 文件
* [HDInsight 文件](https://azure.microsoft.com/documentation/services/hdinsight/)：Azure HDInsight 上的 Hadoop 文件頁面，其中包含文章、視訊和其他資源的連結。
* [開始使用 HDInsight 中的 Hadoop](hdinsight-hadoop-linux-tutorial-get-started.md)：佈建 HDInsight Hadoop 叢集以及執行範例 Hive 查詢的快速入門教學課程。
* [開始使用 HDInsight 中的 Spark](hdinsight-apache-spark-jupyter-spark-sql.md)︰建立 Spark 叢集和執行互動式 Spark SQL 查詢的快速入門教學課程。
* [在 HDInsight 上使用 R 伺服器](hdinsight-hadoop-r-server-get-started.md)︰開始在 HDInsight Premium 中使用 R 伺服器。
* [佈建 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)：了解如何透過 Azure 入口網站、Azure CLI 或 Azure PowerShell 佈建 HDInsight Hadoop 叢集。

### <a name="apache-hadoop"></a>Apache Hadoop
* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>：深入了解 Apache Hadoop 軟體程式庫，這是能夠進行電腦叢集大型資料集分散式處理的架構。
* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a>：深入了解 Hadoop 分散式檔案系統的架構和設計，這是 Hadoop 應用程式使用的主要儲存系統。
* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce 教學課程</a>：深入了解撰寫 Hadoop 應用程式的程式設計架構，這能夠對於大型運算節點叢集快速平行處理大量資料。

### <a name="microsoft-business-intelligence"></a>Microsoft 商業智慧
熟悉的商業智慧 (BI) 工具 (例如 Excel、PowerPivot、SQL Server Analysis Services 及 SQL Server Reporting Services) 可整合使用 Power Query 增益集或 Microsoft Hive ODBC Driver 的 HDInsight 來擷取、分析和報告資料。

這些 BI 工具可協助您分析巨量資料：

* [使用 Power Query 將 Excel 連接到 Hadoop](hdinsight-connect-excel-power-query.md)：了解如何使用 Microsoft Power Query for Excel，將 Excel 連接到儲存 HDInsight 叢集相關資料的 Azure 儲存體帳戶。 必要的 Windows 工作站。 使用在 Linux 或 Windows 上的叢集。
* [使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 Hadoop](hdinsight-connect-excel-hive-odbc-driver.md)：了解如何使用 Microsoft Hive ODBC 驅動程式從 HDInsight 匯入資料。 必要的 Windows 工作站。 使用在 Linux 或 Windows 上的叢集。
* [Microsoft 雲端平台](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx)：了解 Power BI for Office 365、下載 SQL Server 試用版，以及設定 SharePoint Server 2013 和 SQL Server BI。
* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx)。
* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx)。

[marketing-page]: https://azure.microsoft.com/services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/



<!--HONumber=Dec16_HO2-->


