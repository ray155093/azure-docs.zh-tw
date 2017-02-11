---
title: "從 Azure Data Factory 叫用 Spark 程式"
description: "了解如何從 Azure Data Factory 使用 MapReduce 活動叫用 Spark 程式。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: b3895dbf56c58e8e10e6af84afc520e5aef6e01e
ms.openlocfilehash: c2eb07b83f3096f86772f9af63a48da79ce0fd2e


---
# <a name="invoke-spark-programs-from-data-factory"></a>從 Data Factory 叫用 Spark 程式
## <a name="introduction"></a>簡介
您可以使用 Data Factory 管線的 MapReduce 活動，在 HDInsight Spark 叢集上執行 Spark 程式。 如需有關使用活動的詳細資訊，請在閱讀本文之前先參閱 [MapReduce 活動](data-factory-map-reduce.md) 一文。 

## <a name="spark-sample-on-github"></a>GitHub 上的 Spark 範例
[Spark - Data Factory sample on GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) 示範如何使用 MapReduce 活動叫用 Spark 程式。 Spark 程式只是將資料從一個 Azure Blob 容器複製到另一個。 

## <a name="data-factory-entities"></a>Data Factory 實體
**Spark-ADF/src/ADFJsons** 資料夾包含 Data Factory 實體的檔案 (連結的服務、資料集、管線)。  

這個範例有兩個 **連結服務** ︰Azure 儲存體和 Azure HDInsight。 在 **StorageLinkedService.json** 中指定 Azure 儲存體名稱和金鑰值，以及在 **HDInsightLinkedService.json** 中指定 clusterUri、userName 和密碼。

這個範例中有兩個**資料集**︰**input.json** 和 **output.json**。 這些檔案都位於 [資料集]  資料夾中。  這些檔案代表 MapReduce 活動的輸入和輸出資料集

您可以在 **ADFJsons/Pipeline** 資料夾中找到範例管線。 請檢閱管線以了解如何使用 MapReduce 活動來叫用 Spark 程式。 

MapReduce 活動被設定為叫用 Azure 儲存體之 **adflibs** 容器中的 **com.adf.sparklauncher.jar** (在 StorageLinkedService.json 中指定)。 這個程式的原始程式碼位於 Spark-ADF/src/main/java/com/adf/ 資料夾中，它會呼叫 spark-submit 並執行 Spark 作業。 

> [!IMPORTANT]
> 在使用範例之前，請先詳閱 [README.TXT](https://github.com/Azure/Azure-DataFactory/blob/master/Samples/Spark/README.md) 以了解最新資訊及其他資訊。 
> 
> 請搭配這個方法使用您自己的 HDInsight Spark 叢集，以使用 MapReduce 活動來叫用 Spark 程式。 不支援使用隨選 HDInsight 叢集。   
> 
> 

## <a name="see-also"></a>另請參閱
* [Hive 活動](data-factory-hive-activity.md)
* [Pig 活動](data-factory-pig-activity.md)
* [MapReduce 活動](data-factory-map-reduce.md)
* [Hadoop 串流活動](data-factory-hadoop-streaming-activity.md)
* [叫用 R 指令碼](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)




<!--HONumber=Nov16_HO3-->


