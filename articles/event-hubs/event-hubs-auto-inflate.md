---
title: "自動相應增加 Azure 事件中樞輸送量單位 | Microsoft Docs"
description: "在命名空間上啟用自動擴充以自動相應增加輸送量單位"
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2017
ms.author: shvija;sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: b085091ea7bfd601efb0eee84144ddd091422d6e
ms.contentlocale: zh-tw
ms.lasthandoff: 06/13/2017


---

# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>自動相應增加 Azure 事件中樞輸送量單位

## <a name="overview"></a>概觀

Azure 事件中樞為可高度擴充的資料串流平台。 因此，事件中樞客戶通常會在上線至該服務之後提升他們的使用量。 提升使用量必須提升預先設定的輸送量單位 (TU)，以調整事件中樞並處理較大的傳輸速率。 事件中樞的「自動擴充」功能可以自動相應增加 TU 數目以符合使用量需求。 提升 TU 能避免節流案例，在這些案例中：

* 資料輸入速率會超過所設定的 TU。
* 資料輸出要求速率會超過所設定的 TU。

## <a name="how-auto-inflate-works"></a>自動擴充的運作方式

事件中樞的流量會受到輸送量單位控制。 單一 TU 可允許每秒 1 MB 的輸入，以及兩倍數量的輸出。 標準事件中樞可以設定為 1-20 個輸送量單位。 自動擴充可讓您於開始時使用最小的必要輸送量單位。 該功能接著會根據流量的提升，自動擴充至您所需的輸送量單位上限。 自動擴充提供下列優點：

- 能視需求進行相應增加的有效擴充機制。
- 自動擴充至指定的上限，以避免產生節流問題。
- 透過控制調整的時機和程度，來取得更多擴充上的控制。

## <a name="enable-auto-inflate-on-a-namespace"></a>在命名空間上啟用自動擴充

您可以使用下列其中一種方法，在命名空間上啟用或停用自動擴充：

1. [Azure 入口網站](https://portal.azure.com)。
2. Azure Resource Manager 範本。

### <a name="enable-auto-inflate-through-the-portal"></a>透過入口網站啟用自動擴充

您可以在建立事件中樞命名空間時，在命名空間上啟用自動擴充功能：
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

啟用此選項時，您可以於開始時使用較小的輸送量單位，並隨著使用量需求的提升進行相應增加。 擴充的上限並不會影響價格，因為價格是依每小時所使用的 TU 數目來計算。

您也可以使用入口網站中 [設定] 刀鋒視窗上的 [調整] 選項來啟用自動擴充：
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate2.png)

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本啟用自動擴充

您可以在 Azure Resource Manager 範本部署期間啟用自動擴充。 例如，將 `isAutoInflateEnabled` 屬性設定為 **true**，並將 `maximumThroughputUnits` 設定為 10。

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

如需完整的範本，請參閱 GitHub 上的[建立事件中樞命名空間並啟用擴充](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) \(英文\) 範本。

## <a name="next-steps"></a>後續步驟

您可以造訪下列連結以深入了解事件中樞︰

* [事件中樞概觀](event-hubs-what-is-event-hubs.md)
* [建立事件中樞](event-hubs-create.md)

