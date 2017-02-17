---
title: "封存遙測資料與 Azure 事件中樞封存概觀 | Microsoft Docs"
description: "Azure 事件中樞封存功能的概觀。"
services: event-hubs
documentationcenter: 
author: djrosanova
manager: timlt
editor: 
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2016
ms.author: darosa;sethm
translationtype: Human Translation
ms.sourcegitcommit: ca66a344ea855f561ead082091c6941540b1839d
ms.openlocfilehash: 7f5652aa39d6681b4a96cac00daac904dce2e537


---
# <a name="azure-event-hubs-archive"></a>Azure 事件中樞封存
Azure 事件中樞封存可讓您自動將事件中樞的串流資料傳遞到您選擇的 Blob 儲存體帳戶，並另外增加了可指定所選擇的時間或大小間隔的彈性。 設定封存的作業很快，因此執行時不需要系統管理成本，而且它可以使用事件中樞 [輸送量單位](event-hubs-what-is-event-hubs.md#capacity)自動調整。 事件中樞封存是將串流資料載入至 Azure 的最簡單方式，並可讓您專注於處理資料而非擷取資料。

Azure 事件中樞封存可讓您在相同串流上處理即時和批次型的管線。 這可讓您建置可隨時間配合需求成長的方案。 不論您現在是要建置著眼於未來即時處理的批次型系統，或想要為現有即時方案新增有效率的冷路徑，事件中樞封存都可以讓使用串流資料變得更簡單。

## <a name="how-event-hubs-archive-works"></a>事件中樞封存的運作方式
事件中樞是用於輸入遙測的時間保留持久緩衝區，類似於分散式記錄。 在事件中樞調整大小的關鍵是 [資料分割取用者模型](event-hubs-what-is-event-hubs.md#partitions)。 每個資料分割都是獨立的資料區段，可獨立取用。 根據可設定的保留期限，此資料會隨時間而過時。 因此，給定的事件中樞永遠不會「太滿」。

事件中樞封存可讓您指定您自己的 Azure Blob 儲存體帳戶和容器，以用來儲存封存的資料。 此帳戶可以和事件中樞位於相同區域，也可以位於另一個區域，以增加事件中樞封存功能的彈性。

封存的資料會以 [Apache Avro][Apache Avro] 格式寫入，此為精簡、快速、二進位的格式，可使用內嵌結構描述提供豐富的資料結構。 此格式廣泛運用在 Hadoop 生態系統，且為串流分析和 Azure Data Factory 所廣泛使用。 關於使用 Avro 的詳細資訊可在本文稍後看到。

### <a name="archive-windowing"></a>封存範圍
事件中樞封存可讓您設定要控制封存的範圍。 此範圍是具有「先者勝出原則」的最小大小和時間組態，這表示所遇到的第一個觸發會導致封存作業。 如果您有 15 分鐘/100 MB 封存範圍且傳送速率為 1 MB/秒，大小範圍會比時間範圍更早觸發。 每個資料分割都會獨立封存，並在封存時寫入已完成的區塊 Blob，而且會以遇到封存間隔的時間命名。 命名慣例如下︰

```
[Namespace]/[EventHub]/[Partition]/[YYYY]/[MM]/[DD]/[HH]/[mm]/[ss]
```

### <a name="scaling-to-throughput-units"></a>調整至輸送量單位
事件中樞的流量會受到 [輸送量單位](event-hubs-what-is-event-hubs.md#capacity)控制。 單一輸送量單位可允許每秒 1 MB 或每秒 1000 個事件的輸入，輸出則為此數量的兩倍。 標準事件中樞可以設定 1-20 個輸送量單位，您可以透過增加配額的[支援要求][support request]購買更多單位。 超過所購買輸送量單位的使用量將遭到節流。 事件中樞封存會直接從內部的事件中樞儲存體複製資料，略過輸送量單位輸出配額，並將輸出節省下來以供串流分析或 Spark 等其他處理讀取器使用。

事件中樞封存在經過設定之後，就會自動在您傳送第一個事件時立即執行。 它會一直保持執行。 為了讓下游處理更輕鬆地知道處理程序正在運作，事件中樞會在沒有資料時寫入空白檔案。 這會提供可預測的頻率和標記，以供饋送給批次處理器。

## <a name="setting-up-event-hubs-archive"></a>設定事件中樞封存
在建立事件中樞時，可透過入口網站或 Azure Resource Manager 設定事件中樞封存。 只要按一下 [開啟]  按鈕就可以啟用封存。 按一下刀鋒視窗的 [容器]  區段，就可以設定儲存體帳戶和容器。 事件中樞封存會對儲存體使用服務對服務驗證，因此您不需要指定儲存體連接字串。 資源選擇器會自動選取儲存體帳戶的資源 URI。 如果您使用 Azure Resource Manager，您必須以字串形式明確提供此 URI。

預設時間範圍為 5 分鐘。 最小值是 1，最大值是 15。 **大小** 範圍為 10-500 MB。

![][1]

## <a name="adding-archive-to-an-existing-event-hub"></a>將封存功能新增至現有事件中樞
您可以在位於事件中樞命名空間的現有事件中樞上設定封存功能。 此功能無法在舊版的**傳訊**或**混合**類型命名空間上使用。 若要在現有事件中樞啟用封存功能，或變更您的封存設定，請按一下您的命名空間以載入 [基本功能] 刀鋒視窗，然後按一下您要啟用或變更封存設定的事件中樞。 最後，如下圖所示按一下開啟之刀鋒視窗的 [屬性]  區段。

![][2]

您也可以透過 Azure Resource Manager 範本來設定事件中樞封存。 如需詳細資訊，請參閱 [本篇文章](event-hubs-resource-manager-namespace-event-hub-enable-archive.md)。

## <a name="exploring-the-archive-and-working-with-avro"></a>瀏覽封存並使用 Avro
事件中樞封存在設定之後，會在所設定的時間範圍內提供的 Azure 儲存體帳戶和容器中建立檔案。 您可以在任何工具 (例如 [Azure 儲存體總管][Azure Storage Explorer]) 檢視這些檔案。 您可以在本機下載檔案，以對其進行處理。

事件中樞封存所產生的檔案會有下列 Avro 結構描述︰

![][3]

瀏覽 Avro 檔案的簡易方式是使用 Apache 所提供的 [Avro Tools][Avro Tools] jar。 下載這個 jar 之後，您可以執行下列命令來查看特定 Avro 檔案的結構描述︰

```
java -jar avro-tools-1.8.1.jar getschema \<name of archive file\>
```

此命令會傳回

```
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

您也可以使用 Avro Tools 將檔案轉換成 JSON 格式，並執行其他處理。

若要執行更進階的處理，請下載並安裝您所選平台適用的 Avro。 在本文撰寫當下，已有適用於 C、C++、C\#、Java、NodeJS、Perl、PHP、Python 和 Ruby 的實作。

Apache Avro 已完成適用於 [Java][Java] 和 [Python][Python] 的快速入門指南。 您也可以閱讀 [開始使用事件中樞封存](event-hubs-archive-python.md) 一文。

## <a name="how-event-hubs-archive-is-charged"></a>事件中樞封存的收費方式
事件中樞封存的計量方式類似輸送量單位，屬於每小時的費用。 其費用與命名空間所購買的輸送量單位數目成正比。 當輸送量單位增加和減少時，事件中樞封存也會增加和減少以提供相符的效能。 計量會一起發生。 事件中樞封存的費用是每小時每輸送量單位 0.10 美元，預覽期間享有 50% 折扣。

事件中樞封存是將資料送入 Azure 的最簡單方式。 使用 Azure Data Lake、Azure Data Factory 和 Azure HDInsight，即可使用熟悉的工具和平台，以您需要的規模執行您所選擇的批次處理和其他分析。

## <a name="next-steps"></a>後續步驟
您可以造訪下列連結以深入了解事件中樞︰

* [使用事件中樞的完整範例應用程式][sample application that uses Event Hubs]。
* [使用事件中樞相應放大事件處理][Scale out Event Processing with Event Hubs]範例。
* [事件中樞概觀][Event Hubs overview]

[Apache Avro]: http://avro.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[1]: ./media/event-hubs-archive-overview/event-hubs-archive1.png
[2]: media/event-hubs-archive-overview/event-hubs-archive2.png
[Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-archive-overview/event-hubs-archive3.png
[Avro Tools]: http://www-us.apache.org/dist/avro/avro-1.8.1/java/avro-tools-1.8.1.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Jan17_HO4-->


