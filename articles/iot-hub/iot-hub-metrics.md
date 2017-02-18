---
title: "使用計量監視 Azure IoT 中樞 | Microsoft Docs"
description: "如何使用 Azure IoT 中樞度量來評估和監視 IoT 中樞的整體健全狀況。"
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a47108fd-f994-4105-b21d-5b8f697b699c
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: e8cac4af4b971320429cc4c76b8d806e314e1143


---
# <a name="iot-hub-metrics"></a>IoT 中樞計量
IoT 中樞度量提供更好的資料，讓您了解 Azure 訂用帳戶中各種 Azure 資源的狀態。 IoT 中樞度量可讓您評估 IoT 中樞服務及其連接之裝置的整體健全狀況。 提供給您的統計資料非常重要，因為它們可以協助您了解其 IoT 中樞的情況，並協助您不需要連絡 Azure 支援人員就解決根本問題。

預設會啟用計量。 您可以從 Azure 入口網站啟用 IoT 中樞計量。

## <a name="how-to-view-iot-hub-metrics"></a>如何檢視 IoT 中樞計量
1. 建立 IoT 中樞。 您可以在[開始使用][lnk-get-started]指南中找到如何建立 IoT 中樞的指示。
2. 開啟 IoT 中樞的刀鋒視窗。 按一下此處的 [計量]。
   
    ![][1]
3. 您可以在 [計量] 刀鋒視窗中檢視 IoT 中樞的計量，並建立計量的自訂檢視。 您可以藉由按一下 [診斷設定]，選擇將計量資料傳送到事件中樞端點或 Azure 儲存體帳戶。
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT 中樞度量及其使用方式
IoT 中樞提供數個度量，以讓您概略了解中樞的健全狀況和所連線裝置的總數。 您可以結合多個度量的資訊，以便更清楚地了解 IoT 中樞的狀態。 下表描述每個 IoT 中樞所追蹤的度量，以及每個度量與 IoT 中樞整體狀態的關聯。

| 度量 | 度量說明 | 度量用途 |
| --- | --- | --- |
| d2c.telemetry.ingress.allProtocol | 所有裝置上傳送的訊息數目 | 關於訊息傳送的概觀資料 |
| d2c.telemetry.ingress.success | 成功進入中樞的所有訊息數目 | 成功進入中樞之訊息的概觀 |
| d2c.telemetry.egress.success | 端點的所有成功寫入計數 | 根據使用者的路由展開傳送訊息的概觀 |
| d2c.telemetry.egress.invalid | 因為與端點不相容而未傳遞的訊息計數 | 寫入使用者的端點集合時產生多少次失敗的概觀。 高值可能表示未正確設定的端點。 |
| d2c.telemetry.egress.dropped | 因為端點狀況不良而捨棄的訊息計數 | 已根據目前的 IoT中樞組態捨棄了多少個訊息的概觀 |
| d2c.telemetry.egress.fallback | 符合後援路由的訊息計數 | 若使用者會使用管線將所有訊息傳送至內建端點以外的其他端點，則此計量會顯示路由傳送設定中的間距 |
| d2c.telemetry.egress.orphaned | 不符合任何路由 (包括後援路由) 的訊息計數 | 已根據目前的 IoT中樞組態孤立了多少個訊息的概觀 |
| d2c.endpoints.latency.eventHubs | 訊息輸入到 IoT 中樞與訊息輸入到事件中樞端點之間的平均延遲，以毫秒為單位 | 此分配可協助使用者識別不佳的端點組態 |
| d2c.endpoints.latency.serviceBusQueues | 訊息輸入到 IoT 中樞與訊息輸入到服務匯流排佇列端點之間的平均延遲，以毫秒為單位 | 此分配可協助使用者識別不佳的端點組態 |
| d2c.endpoints.latency.serviceBusTopic | 訊息輸入到 IoT 中樞與訊息輸入到服務匯流排主題端點之間的平均延遲，以毫秒為單位 | 此分配可協助使用者識別不佳的端點組態 |
| d2c.endpoints.latency.builtIn.events | 訊息輸入到 IoT 中樞與訊息輸入到內建端點 (訊息/事件) 之間的平均延遲，以毫秒為單位 | 此分配可協助使用者識別不佳的端點組態 |
| c2d.commands.egress.complete.success | 接收端裝置在所有裝置上完成的所有命令訊息數目 |搭配關於放棄或拒絕的度量，可提供整體雲端到裝置命令成功率概觀 |
| c2d.commands.egress.abandon.success | 接收端裝置在所有裝置上成功放棄的所有訊息數目 |如果訊息比預期的更常遭到放棄，則會點出潛在問題 |
| c2d.commands.egress.reject.success | 接收端裝置在所有裝置上成功拒絕的所有訊息數目 |如果訊息比預期的更常遭到拒絕，則會點出潛在問題 |
| devices.totalDevices | 向 IoT 中樞註冊的裝置數目計數 |向中樞註冊的裝置數目 |
| devices.connectedDevices.allProtocol | 同時連接之裝置的數目計數 |連接到中樞之裝置數目的概觀 |

## <a name="next-steps"></a>後續步驟
現在您已了解 IoT 中樞度量的概觀，請循著下列連結來深入了解如何管理「Azure IoT 中樞」：

* [作業監視][lnk-monitor]

若要進一步探索 IoT 中樞的功能，請參閱︰

* [IoT 中樞開發人員指南][lnk-devguide]
* [使用 IoT 閘道 SDK 來模擬裝置][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Jan17_HO4-->


