---
title: "IoT 中樞的診斷度量"
description: "Azure IoT 中樞度量的概觀，可讓您評估其資源的整體健全狀況"
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
ms.sourcegitcommit: ce514e19370d2b42fb16b4e96b66f212d5fa999c
ms.openlocfilehash: e2739f9f47495c4502af3aff95d2d3f3975a881b


---
# <a name="introduction-to-diagnostic-metrics"></a>診斷度量簡介
診斷度量可提供有關您 Azure 訂用帳戶中 Azure 資源狀態的更好資料。 度量可讓您評估服務以及連接到服務之裝置的整體健全狀況。 提供給您的統計資料非常重要，因為它們可以協助您了解其 IoT 中樞的情況，並協助您不需要連絡 Azure 支援人員就解決根本問題。

您可以從 Azure 入口網站啟用診斷度量。

## <a name="how-to-enable-diagnostic-metrics"></a>如何啟用診斷度量
1. 建立 IoT 中樞。 您可以在[開始使用][lnk-get-started]指南中找到如何建立 IoT 中樞的指示。
2. 開啟 IoT 中樞的刀鋒視窗。 按一下其中的 [診斷] 。
   
    ![][1]
3. 將狀態設為 [開啟]，並選取要用來儲存診斷資料的「Azure 儲存體」帳戶，以設定診斷。 勾選 [度量]，然後按 [儲存]。 請注意，您必須事先建立「Azure 儲存體」帳戶，而且需要針對儲存體個別支付費用。 您也可以選擇將您的診斷資料傳送至事件中樞端點。
   
    ![][2]
4. 設定好診斷後，返回 [概觀]  IoT 中樞刀鋒視窗。 刀鋒視窗的 [監視]  區段中就會填入度量資訊。 按一下圖表便會開啟度量窗格，您可以在此檢視 IoT 中樞的度量資訊摘要。 您可以編輯圖表中顯示的選取度量，並設定以度量值為基礎的警示。
   
    ![][3]

## <a name="metrics-and-how-to-use-them"></a>度量及其使用方式
IoT 中樞提供數個度量，以讓您概略了解中樞的健全狀況和所連線裝置的總數。 您可以結合多個度量的資訊，以便更清楚地了解 IoT 中樞的狀態。 下表描述每個 IoT 中樞所追蹤的度量，以及每個度量與 IoT 中樞整體狀態的關聯。

| 度量 | 度量說明 | 度量用途 |
| --- | --- | --- |
| d2c.telemetry.ingress.allProtocol |所有裝置上傳送的訊息數目 |關於訊息傳送的概觀資料 |
| d2c.telemetry.ingress.success |成功進入 IoT 中樞的所有訊息數目 |成功進入 IoT 中樞之訊息的概觀 |
| c2d.commands.egress.complete.success |接收端裝置在所有裝置上完成的所有命令訊息數目 |搭配關於放棄或拒絕的度量，可提供整體雲端到裝置命令成功率概觀 |
| c2d.commands.egress.abandon.success |接收端裝置在所有裝置上成功放棄的所有訊息數目 |如果訊息比預期的更常遭到放棄，則會點出潛在問題 |
| c2d.commands.egress.reject.success |接收端裝置在所有裝置上成功拒絕的所有訊息數目 |如果訊息比預期的更常遭到拒絕，則會點出潛在問題 |
| devices.totalDevices |向 IoT 中樞註冊之裝置的平均數目、最小數目和最大數目 |向 IoT 中樞註冊的裝置數目 |
| devices.connectedDevices.allProtocol |同時連接之裝置的平均數目、最小數目和最大數目 |連接到 IoT 中樞之裝置數目的概觀 |

## <a name="next-steps"></a>後續步驟
現在您已了解診斷度量的概觀，接下來請遵循下列連結來深入了解如何管理「Azure IoT 中樞」：

* [作業監視][lnk-monitor]

若要進一步探索 IoT 中樞的功能，請參閱︰

* [開發人員指南][lnk-devguide]
* [使用 IoT 閘道 SDK 來模擬裝置][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png
[3]: media/iot-hub-metrics/enable-metrics-3.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Nov16_HO5-->


