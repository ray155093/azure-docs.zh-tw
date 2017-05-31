---
title: "使用 Apache Storm on HDInsight 處理車輛感應器資料 | Microsoft Docs"
description: "了解如何使用 Apache Storm on HDInsight 處理事件中樞的車輛感應器資料 從 Azure Cosmos DB 加入型號資料，並將輸出儲存至儲存體。"
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
ms.date: 05/03/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8e8ebc724e1c70e8fcd56312adef5da2342373ea
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="process-vehicle-sensor-data-from-azure-event-hubs-using-apache-storm-on-hdinsight"></a>使用 Apache Storm on HDInsight 處理 Azure 事件中樞的車輛感應器資料

了解如何使用 Apache Storm on HDInsight 處理 Azure 事件中心的車輛感應器資料 此範例會從 Azure 事件中樞讀取感應器資料，藉由參考儲存在 Azure Cosmos DB 中的資料來擴充資料。 資料會使用 Hadoop 檔案系統 (HDFS) 儲存到 Azure 儲存體。

![HDInsight 和物聯網 (IoT) 架構圖表](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

## <a name="overview"></a>概觀

新增車輛的感應器可讓您根據歷程記錄資料的趨勢預測設備問題。 它也可讓您根據使用模式分析來改良未來的版本。 您必須能在 MapReduce 處理發生之前，快速且有效地將來自所有車輛的資料載入 Hadoop。 此外，您可能會想要執行嚴重失敗路徑 (引擎溫度、煞車等) 的即時分析。

Azure 事件中樞是建置來處理感應器所產生的大量資料。 Apache Storm 則可用來載入和處理資料，再將資料儲存到 HDFS。

## <a name="solution"></a>方案

感應器會記錄引擎溫度、周圍溫度和車輛速度的遙測資料。 然後，資料會連同車輛識別碼 (VIN) 和時間戳記一併傳送至事件中樞。 從事件中心，在 Apache Storm on HDInsight 叢集上執行的 Storm 拓撲會讀取該資料、加以處理，並將其儲存到 HDFS。

在處理期間，VIN 可用來從 Cosmos DB 擷取型號資訊。 此資料會在儲存前加入至資料流。

Storm 拓撲中使用的元件如下：

* **EventHubSpout** - 讀取 Azure 事件中心的資料
* **TypeConversionBolt** - 將來自事件中樞的 JSON 字串轉換成包含下列感應器資料的 Tuple︰
    * Engine temperature
    * 周圍溫度
    * 速度
    * VIN
    * Timestamp
* **DataReferencBolt** - 使用 VIN 從 Cosmos DB 查閱車輛型號
* **WasbStoreBolt** - 將資料儲存到 HDFS (Azure 儲存體)

下圖是此方案的圖表：

![Storm 拓撲](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

## <a name="implementation"></a>實作

本案例中已完成的自動化方案可在 GitHub 上作為 [HDInsight-Storm-Examples](https://github.com/hdinsight/hdinsight-storm-examples) 儲存機制的一部分。 若要使用此範例，請遵循 [IoTExample README.MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md)中的步驟。

## <a name="next-steps"></a>後續步驟

若需更多範例 Storm 拓撲，請參閱 [Storm on HDInsight 上的範例拓撲](hdinsight-storm-example-topology.md)。


