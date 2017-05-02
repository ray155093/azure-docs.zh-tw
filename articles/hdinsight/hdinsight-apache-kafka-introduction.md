---
title: "HDInsight 上的 Apache Kafka 簡介 | Microsoft Docs"
description: "了解 HDInsight 上 Apache Kafka。 它是什麼、其用途以及到何處尋找範例和入門資訊。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: f284b6e3-5f3b-4a50-b455-917e77588069
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: a3ceca6cd0f470a5cd6849c345867f094b870a85
ms.lasthandoff: 04/20/2017

---
# <a name="introducing-apache-kafka-on-hdinsight-preview"></a>HDInsight 上的 Apache Kafka (預覽) 簡介

[Apache Kafka](https://kafka.apache.org) 是開放原始碼分散式串流平台，可用來建置即時串流資料管線和應用程式。 Kafka 也提供類似於訊息佇列的訊息代理程式功能，可讓您發佈和訂閱具名資料流。 在 HDInsight 上的 Kafka 為您在 Microsoft Azure 雲端中提供受管理、高可調整性和高可用性的服務。

## <a name="why-use-kafka-on-hdinsight"></a>為何使用 HDInsight 上的 Kafka？

Kafka 提供下列功能：

* 發佈-訂閱傳訊模式︰Kafka 提供生產者 API，可將記錄發佈到 Kafka 主題。 訂閱主題時會使用取用者 API。

* 串流處理︰Kafka 通常與 Apache Storm 或 Spark 一起用來處理即時串流。 Kafka 0.10.0.0 (HDInsight 3.5 版) 引進串流 API，讓您不需要 Storm 或 Spark 就能建置串流解決方案。

* 水平縮放︰Kafka 可將串流分割給 HDInsight 叢集的各節點。 取用者處理程序可以與個別的資料分割相關聯，在取用記錄時可平衡負載。

* 依序傳遞︰在每個資料分割內，記錄會依收到時的順序儲存在串流中。 每個資料分割與一個取用者處理序建立關聯之後，就能保證依序處理記錄。

* 容錯︰節點之間可以複寫資料分割，發揮容錯能力。

## <a name="use-cases"></a>使用案例

* **傳訊**︰Kafka 支援發佈-訂閱傳訊模式，通常作為訊息代理程式。

* **活動追蹤**︰Kafka 能夠依序登載記錄，可用來追蹤和重新建立活動。 例如，網站或應用程式中的使用者動作。

* **彙總**︰您可以利用串流處理來彙總不同串流的資訊，將資訊結合並集中而成為可操作的資料。

* **轉換**︰您可以利用串流處理來結合並充實多個輸入主題的資料，而成為一個或多個輸出主題。

## <a name="where-do-i-start"></a>我該從哪裡開始？

如需有關建立 Kafka 叢集和使用 Kafka 的步驟，包括使用生產者、取用者和串流 API 的 Java 範例，請參閱[開始使用 HDInsight 上的 Kafka](hdinsight-apache-kafka-get-started.md)

## <a name="next-steps"></a>後續步驟

使用下列連結以了解如何使用 HDInsight 上的 Apache Kafka：

* [開始使用 HDInsight 上的 Kafka](hdinsight-apache-kafka-get-started.md)

* [使用 MirrorMaker 建立 Apache Kafka on HDInsight 複本](hdinsight-apache-kafka-mirroring.md)

* [使用 Apache Storm 搭配 HDInsight 上的 Kafka](hdinsight-apache-storm-with-kafka.md)

* [使用 Apache Spark 搭配 Kafka on HDInsight](hdinsight-apache-spark-with-kafka.md)

* [透過 Azure 虛擬網路連線至 Kafka](hdinsight-apache-kafka-connect-vpn-gateway.md)
