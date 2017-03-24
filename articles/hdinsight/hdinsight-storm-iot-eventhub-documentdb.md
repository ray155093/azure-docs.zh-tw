---
title: "使用 Apache Storm on HDInsight 處理車輛感應器資料 | Microsoft Docs"
description: "了解如何使用 Apache Storm on HDInsight 處理事件中樞的車輛感應器資料 從 DocumentDB 加入模型資料，並將輸出儲存至儲存體。"
services: hdinsight,documentdb,notification-hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 78980635-8bef-4c33-96c3-fae50e932e31
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 46bc5b3b70120cd631523fd2b27ad8b9a47e3c6d
ms.openlocfilehash: 952480e71dac19c7772198516863b3e64be1a6b3
ms.lasthandoff: 11/17/2016


---
# <a name="process-vehicle-sensor-data-from-azure-event-hubs-using-apache-storm-on-hdinsight"></a>使用 Apache Storm on HDInsight 處理 Azure 事件中樞的車輛感應器資料

了解如何使用 Apache Storm on HDInsight 處理 Azure 事件中心的車輛感應器資料 本範例會從 Azure 事件中心讀取感應器資料、藉由參考儲存在 Azure DocumentDB 的資料來充實資料，最後使用 Hadoop 檔案系統 (HDFS) 將資料儲存到 Azure 儲存體。

![HDInsight 和物聯網 (IoT) 架構圖表](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

## <a name="overview"></a>概觀

將感應器新增至車輛可讓您根據歷程記錄資料趨勢預測設備問題，並且根據使用模式分析增強未來版本的功能。 雖然傳統 MapReduce 批次處理可以用來進行這項分析，但您必須能在 MapReduce 處理發生之前，快速且有效地將來自所有車輛的資料載入 Hadoop。 此外，您可能會想要執行嚴重失敗路徑 (引擎溫度、煞車等) 的即時分析。

Azure 事件中心是建置來處理由感應器產生的極大量資料，而 Apache Storm on HDInsight 則可用來先載入與處理資料，再將其儲存到 HDFS (由 Azure 儲存體支援) 進行其他 MapReduce 處理。

## <a name="solution"></a>方案

感應器可記錄引擎溫度、周圍溫度和車輛速度等遙測資料，然後將其和車子的車輛識別碼 (VIN) 及時間戳記一起傳送到事件中心。 從事件中心，在 Apache Storm on HDInsight 叢集上執行的 Storm 拓撲會讀取該資料、加以處理，並將其儲存到 HDFS。

在處理期間，VIN 可用來從 Azure DocumentDB 擷取模型資訊。 這個資訊會在儲存前加入至資料流。

Storm 拓撲中使用的元件如下：

* **EventHubSpout** - 讀取 Azure 事件中心的資料
* **TypeConversionBolt** - 將 JSON 字串從事件中心轉換為 tuple，其中包含引擎溫度、周圍溫度、速度、VIN 和時間戳記的個別資料值
* **DataReferencBolt** - 使用 VIN 從 DocumentDB 查閱車輛模型
* **WasbStoreBolt** - 將資料儲存到 HDFS (Azure 儲存體)

以下是此方案的圖表：

![Storm 拓撲](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

> [!NOTE]
> 這是一個簡化的圖表，且在方案中的每個元件都可能會有多個執行個體。 例如，在拓撲中每個元件的多個執行個體會跨節點分布在 Storm on HDInsight 叢集中。
> 
> 

## <a name="implementation"></a>實作

本案例中已完成的自動化方案可在 GitHub 上作為 [HDInsight-Storm-Examples](https://github.com/hdinsight/hdinsight-storm-examples) 儲存機制的一部分。 若要使用此範例，請遵循 [IoTExample README.MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md)中的步驟。

## <a name="next-steps"></a>後續步驟

若需更多範例 Storm 拓撲，請參閱 [Storm on HDInsight 上的範例拓撲](hdinsight-storm-example-topology.md)。


