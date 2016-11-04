---
title: 整合資料湖存放區與其他 Azure 服務 | Microsoft Docs
description: 了解資料湖存放區如何與其他 Azure 服務整合
documentationcenter: ''
services: data-lake-store
author: nitinme
manager: jhubbard
editor: cgronlun

ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/02/2016
ms.author: nitinme

---
# 整合資料湖存放區與其他 Azure 服務
Azure 資料湖存放區可以與其他 Azure 服務一起使用，以啟用更廣泛的案例。下列文章列出資料湖存放區可以整合的服務。

## 搭配 Azure HDInsight 使用資料湖存放區
您可以佈建 [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) 叢集，其使用資料湖存放區做為 HDFS 相容儲存體。對於此版本，Windows 和 Linux 上的 Hadoop 和 Storm 叢集，您只能使用資料湖存放區做為額外的儲存體。這類叢集仍會使用 Azure 儲存體 (WASB) 做為預設儲存體。但是，對於 Windows 和 Linux 上的 HBase 叢集，您可以使用資料湖存放區做為預設儲存體或額外的儲存體，或同時做為兩者。

如需如何使用資料湖存放區佈建 HDInsight 叢集的指示，請參閱：

* [使用 Azure 入口網站佈建 HDInsight 叢集與資料湖存放區](data-lake-store-hdinsight-hadoop-use-portal.md)
* [使用 Azure PowerShell 佈建 HDInsight 叢集與資料湖存放區](data-lake-store-hdinsight-hadoop-use-powershell.md)

**偏好影片？** 請遵循下列連結，以觀看影片說明如何使用 Data Lake Store 搭配 HDInsight 叢集。

* [建立可存取 Data Lake Store 的 HDInsight 叢集](https://mix.office.com/watch/l93xri2yhtp2)
* 一旦設定叢集，[請使用 Hive 和 Pig 指令碼存取 Data Lake Store 中的資料](https://mix.office.com/watch/1n9g5w0fiqv1q)

## 搭配 Azure 資料湖分析使用資料湖存放區
[Azure 資料湖分析](../data-lake-analytics/data-lake-analytics-overview.md)可讓您在雲端規模使用巨量資料。它以動態方式佈建資源，讓您能夠進行分析 TB 或甚至是 EB 的資料，這些資料可以儲存在許多支援的資料來源，其中一個就是資料湖存放區。資料湖分析已特別最佳化以使用 Azure 資料湖存放區 - 提供最高層級的效能、輸送量和您的巨量資料工作負載的平行處理。

如需有關如何搭配資料湖存放區使用資料湖分析的指示，請參閱[使用資料湖存放區開始使用資料湖分析](../data-lake-analytics/data-lake-analytics-get-started-portal.md)。

**偏好影片？** 請遵循下列連結，以觀看影片說明如何使用 Data Lake Store 搭配 HDInsight 叢集。

* [將 Azure Data Lake Analytics 連接到 Azure Data Lake Store](https://mix.office.com/watch/qwji0dc9rx9k)
* [透過 Data Lake Analytics 存取 Azure Data Lake Store](https://mix.office.com/watch/1n0s45up381a8)

## 搭配 Azure Data Factory 使用資料湖存放區
您可以使用 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) 從 Azure 資料表、Azure SQL Database、Azure SQL 資料倉儲、Azure 儲存體 Blob 和內部部署資料庫擷取資料。Azure 生態系統中的首選，Azure Data Factory 可以用來協調從這些來源到 Azure 資料湖存放區的資料擷取。

如需有關如何搭配資料湖存放區使用 Azure Data Factory 的指示，請參閱[使用 Data Factory 移動資料湖存放區的資料](../data-factory/data-factory-azure-datalake-connector.md)。

**再看一次影片！** 請參閱[使用 Azure Data Factory 進行 Azure Data Lake Store 的資料協調](https://mix.office.com/watch/1oa7le7t2u4ka)。

## 將資料從 Azure 儲存體 Blob 複製到資料湖存放區中
Azure 資料湖存放區提供命令列工具 AdlCopy，可讓您將資料從 Azure Blob 儲存體複製到資料湖存放區帳戶。如需詳細資訊，請參閱[將資料從 Azure 儲存體 Blob 複製到 Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)。

## 在 Azure SQL Database 和 Data Lake Store 之間複製資料
您可以使用 Apache Sqoop 在 Azure SQL Database 和 Data Lake Store 之間匯入及匯出資料。如需詳細資訊，請參閱[使用 Sqoop 在 Data Lake Store 和 Azure SQL Database 之間複製資料](data-lake-store-data-transfer-sql-sqoop.md)。

**請觀看此影片**，主題為[使用 Apache Sqoop 在相關來源與 Azure Data Lake Store 之間移動資料](https://mix.office.com/watch/1butcdjxmu114)。

## 使用 Data Lake Store 搭配串流分析
您可以使用 Data Lake Store 做為其中一個使用 Azure 串流分析儲存串流資料的輸出。如需詳細資訊，請參閱[使用 Azure 串流分析將來自 Azure 儲存體 Blob 的資料串流處理至 Data Lake Store](data-lake-store-stream-analytics.md)。

## 使用 Data Lake Store 搭配 Power BI
您可以使用 Power BI 從 Data Lake Store 匯入資料以分析和視覺化資料。如需詳細資訊，請參閱[在 Data Lake Store 中使用 Power BI 分析資料](data-lake-store-power-bi.md)。

## 使用 Data Lake Store 搭配資料目錄
您可以從 Data Lake Store 將資料註冊至 Azure 資料目錄，讓資料可在整個組織找到。如需詳細資訊，請參閱[在 Azure 資料目錄中從 Data Lake Store 註冊資料](data-lake-store-with-data-catalog.md)。

## 另請參閱
* [Azure 資料湖儲存區概觀](data-lake-store-overview.md)
* [使用入口網站開始使用資料湖存放區](data-lake-store-get-started-portal.md)
* [使用 PowerShell 開始使用資料湖存放區](data-lake-store-get-started-powershell.md)

<!---HONumber=AcomDC_0914_2016-->