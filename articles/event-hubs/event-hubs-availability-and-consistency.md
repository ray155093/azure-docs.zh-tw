---
title: "Azure 事件中樞的可用性和一致性 | Microsoft Docs"
description: "如何使用分割區，以便透過 Azure 事件中樞提供最大數量的可用性和一致性。"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/21/2017
ms.author: sethm;jotaub
translationtype: Human Translation
ms.sourcegitcommit: 7587b1bed2f809fa2c4bab78c54396eed778b9ef
ms.openlocfilehash: df2d79fdb4a26509f3c7c1f8f3a8adcaa6b24f9d
ms.lasthandoff: 02/21/2017

---

# <a name="availability-and-consistency-in-event-hubs"></a>事件中樞的可用性和一致性

## <a name="overview"></a>概觀
Azure 事件中樞會使用[分割模型](event-hubs-what-is-event-hubs.md#partitions)，來提升單一事件中樞內的可用性與平行處理。 例如，如果事件中樞有四個分割區，而這其中一個分割區正在負載平衡作業中從某部伺服器移到另一部，則您仍能從其他三個分割區進行傳送及接收。 此外，更多分割區讓您可使用更多並行讀取器來處理資料，因此能夠改善您的彙總輸送量。 了解分散式系統中分割和排序的含意是解決方案設計的重要層面。

為了協助說明排序和可用性之間的權衡取捨，讓我們來看看 [CAP 理論 (英文)](https://en.wikipedia.org/wiki/CAP_theorem)，也稱為 Brewer 的理論。 這個理論指出必須在一致性、可用性及分割區容錯之間選擇一個。

這個理論會定義一致性和可用性，如下所示：
* 分割區容錯 - 即使發生分割區失敗，資料處理系統還是能夠繼續處理資料。
* 可用性 - 非失敗節點會在合理的時間 (不含錯誤或逾時) 內傳回合理的回應。
* 一致性 - 保證讀取會傳回指定用戶端的最新寫入。

## <a name="partition-tolerance"></a>分割區容錯
事件中樞會建置於已分割模型的最上層。 您可以在安裝期間，於事件中樞設定分割區數目，但之後無法變更此值。 由於您必須將分割區與事件中樞搭配使用，因此只需決定應用程式相關的可用性和一致性即可。

## <a name="availability"></a>Availability
開始使用事件中樞的最簡單方式是預設行為。 如果您建立新的 `EventHubClient` 並使用傳送函式，就會在事件中樞的分割區之間自動散發您的事件。 此行為能夠取得最大的執行時間量。

針對需要最大執行時間的使用案例，偏好使用此模型。

## <a name="consistency"></a>一致性
在特殊案例中，事件的順序非常重要。 例如，您可能想要讓後端系統在刪除命令之前先處理更新命令。 在此情況下，您可以在事件上設定分割區索引鍵，或使用 `PartitionSender` 只將事件傳送到特定的分割區。 這麼做可確保在從分割區讀取這些事件時，會依序讀取它們。

利用這種組態時，您必須記住，如果您要傳送的特定分割區無法使用，則您將會收到錯誤回應。 相較之下，如果您不具對單一分割區的同質性，事件中樞服務就會將事件傳送至下一個可用的分割區。

一個可確保排序，同時將執行時間最大化的可行解決方案是，彙總事件做為您事件處理應用程式的一部分。 最簡單的方式是使用自訂的序號屬性來為事件標上戳記。 以下是這類範例：

```csharp
// Get the latest sequence number from your application
var sequenceNumber = GetNextSequenceNumber();
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);
// Send single message async
await eventHubClient.SendAsync(data);
```

先前範例會將事件傳送到事件中樞的其中一個可用分割區，並從您的應用程式設定對應的序號。 這個解決方案會要求您的處理應用程式保留狀態，但會為您的傳送者提供更容易取得的端點。

## <a name="next-steps"></a>後續步驟
您可以造訪下列連結以深入了解事件中樞︰

* [事件中樞概觀](event-hubs-what-is-event-hubs.md)
* [建立事件中樞](event-hubs-create.md)

