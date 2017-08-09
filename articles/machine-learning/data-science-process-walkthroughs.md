---
title: "Team Data Science Process 逐步解說 | Microsoft Docs"
description: "這些逐步解說示範如何將雲端和內部部署工具及服務組合成工作流程或管線，以建立智慧型應用程式。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: aa63d5a5-25ee-4c4b-9a4c-7553b98d7f6e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: a678700884b612cad6281eb8f3b74ce63a0ebb69
ms.openlocfilehash: c0f0ff599909234b2b43f2b8512d77f9053b8a09
ms.contentlocale: zh-tw
ms.lasthandoff: 07/26/2017

---
# <a name="team-data-science-process-walkthroughs"></a>Team Data Science Process 逐步解說
此處分項列出的**端對端逐步解說**，每一個都會示範 Team Data Science Process 針對**特定案例**的步驟。 這些逐步解說說明如何將雲端、內部部署工具及服務組合成工作流程或管線，以建立「智慧型應用程式」。 逐步解說是依據它們使用的**平台**歸納整理： 

- Spark 與 PySpark 和 Scala
- HDInsight (Hadoop)
- Azure Data Lake 
- SQL Server
- SQL 資料倉儲 


## <a name="hdinsight-spark-using-pyspark-and-scala"></a>使用 PySpark 和 Scala 的 HDInsight Spark

- [在 Azure HDInsight 上使用 Spark](machine-learning-data-science-spark-overview.md) 逐步解說會在採用 [Azure HDInsight Spark 叢集](https://azure.microsoft.com/services/hdinsight/)的案例中使用 Team Data Science Process，以對公開提供使用的 NYC 計程車車程和車資資料集的資料進行儲存、探索和特徵工程設計。

- [在 Azure 上使用 Scala 與 Spark](machine-learning-data-science-process-scala-walkthrough.md) 逐步解說會說明如何使用 Scala 搭配 Spark 機器學習程式庫 (MLlib) 和 Azure HDInsight Spark 叢集上的 SparkML 套件，處理受監督的機器學習工作。 它會引導您進行構成 [資料科學程序](http://aka.ms/datascienceprocess)的各項工作︰資料擷取和探索、視覺化、特徵設計、模型化和模型取用。 建立的模型包括羅吉斯和線性迴歸、隨機樹系和漸層停駐推進式決策樹。


## <a name="hdinsight-hadoop"></a>HDInsight Hadoop 

- [使用 HDInsight Hadoop 叢集](machine-learning-data-science-process-hive-walkthrough.md)逐步解說會使用 [Azure HDInsight Hadoop 叢集](https://azure.microsoft.com/services/hdinsight/)，對公開使用的 NYC 計程車車程和車資資料集的資料進行儲存、探索和特徵工程設計。

- [在 1 TB 資料集上使用 Azure HDInsight Hadoop 叢集](machine-learning-data-science-process-hive-criteo-walkthrough.md)逐步解說會提供一個案例，使用 [Azure HDInsight Hadoop 叢集](https://azure.microsoft.com/services/hdinsight/)進行儲存、探索、特徵工程設計，以及縮減來自公開使用 [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) 資料集的取樣資料。


## <a name="azure-data-lake"></a>Azure Data Lake

- [使用 Azure Data Lake 的資料科學](machine-learning-data-science-process-data-lake-walkthrough.md)示範如何使用 Azure Data Lake，在 NYC 計程車車程和車資資料集範例上執行資料探索和二元分類工作，以預測客戶是否給予小費。 


## <a name="sql-server-and-sql-data-warehouse"></a>SQL Server 和 SQL 資料倉儲 

- [使用 SQL 資料倉儲](machine-learning-data-science-process-sqldw-walkthrough.md)逐步解說會示範如何使用 SQL 資料倉儲 (SQL DW)，針對公開使用的 NYC 計程車車程和車資資料集建置和部署機器學習服務的分類和迴歸模型。

- [使用 SQL Server](machine-learning-data-science-process-sql-walkthrough.md) 逐步解說會示範如何使用 SQL Server，針對公開使用的 NYC 計程車車程和車資資料集建置和部署機器學習服務的分類和迴歸模型。

- [使用 SQL Server R Services](https://msdn.microsoft.com/library/mt612857.aspx) 逐步解說會對資料科學家提供 R 程式碼、SQL Server 資料和自訂 SQL 函式的組合，以在 SQL Server 建置和部署 R 模型。

- [使用 T-SQL 與 SQL Server R Services](https://msdn.microsoft.com/library/mt683480.aspx) 逐步解說會對 SQL 程式設計人員提供使用 SQL Server R Services 來建置搭配 Transact-SQL 的進階分析方案的經驗，以操作 R 方案。

- [使用 T-SQL 與 SQL Server Python Services](https://docs.microsoft.com/en-us/sql/advanced-analytics/tutorials/sqldev-in-database-python-for-sql-developers) 逐步解說可向 SQL 程式設計師提供在 SQL Server 中建置機器學習解決方案的經驗。 該逐步解說會說明如何透過將 Python 程式碼新增至預存程序，來將 Python 合併到應用程式中。

## <a name="whats-next"></a>後續步驟
如需能引導您完成在 Azure 中構成資料科學程序之工作的主題概觀，請參閱 [資料科學程序](http://aka.ms/datascienceprocess)。 


