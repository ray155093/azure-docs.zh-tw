---
title: "Azure IoT 中樞 - 開始將 IoT 裝置連線到雲端 | Microsoft Docs"
description: "了解如何將 IoT 裝置連線到 Azure IoT 中樞。 您的裝置可以將遙測傳送到 IoT 中樞，而 IoT 中樞會監視並管理您的裝置。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: "Azure IoT 中樞教學課程"
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: aeb0b665b8295bba30d8c6c47cc88e446693c91f
ms.contentlocale: zh-tw
ms.lasthandoff: 05/01/2017


---
# <a name="azure-iot-hub-get-started-tutorials"></a>Azure IoT 中樞快速入門教學課程

您可以使用 Azure IoT 中樞和 Azure IoT 裝置 SDK 來建置物聯網 (IoT) 解決方案。

* Azure IoT 中樞是雲端中完全受管理的服務，可安全地連線、監視及管理您的 IoT 裝置。 您可以使用 Azure IoT 裝置 SDK 來實作您的 IoT 裝置。
* 使用在更複雜的 IoT 案例中使用 IoT 閘道，您在此案例中必須考慮諸如傳統裝置、頻寬成本、安全性和隱私權原則或邊緣資料處理等因素。 在這些情況下，您可以使用 Azure IoT 閘道 SDK 來建立可將裝置連線到 IoT 中樞的閘道裝置。

## <a name="what-do-the-tutorials-cover"></a>這些教學課程的涵蓋範圍

這些教學課程將為您介紹 Azure IoT 中樞與裝置的 SDK。 這些教學課程涵蓋的常見 IoT 案例會說明 IoT 中樞的功能。 教學課程也將說明如何將 IoT 中樞與其他 Azure 服務和工具結合，建置更強大的 IoT 解決方案。 在教學課程中，您可以選擇使用模擬或實際的 IoT 裝置。 此外，您可以了解如何使用閘道來將裝置連線到您的 IoT 中樞。

## <a name="device-setup-scenario-connect-iot-device-or-gateway-to-azure-iot-hub"></a>裝置設定案例︰將 IoT 裝置或閘道連線至 Azure IoT 中樞

您可以選擇要開始使用的真實或模擬裝置。

| IoT 裝置                       | 程式設計語言 |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Node.js][Pi_Nd]、[C][Pi_C]           |
| Intel Edison                    | [Node.js][Ed_Nd]、[C][Ed_C]           |
| Adafruit Feather HUZZAH ESP8266 | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing 開發人員      | [Arduino][Th_Ard]              |
| Adafruit Feather M0             | [Arduino][M0_Ard]              |
| 模擬裝置                | [.NET][Sim_NET]、[Java][Sim_Jav]、[Node.js][Sim_Nd]、[Python][Sim_Pyth]              |

此外，您可以使用閘道來將裝置連線到您的 IoT 中樞。

| 閘道裝置               | 程式設計語言 | 平台         |
|------------------------------|----------------------|------------------|
| Intel NUC (模型 DE3815TYKE) | C                    | [Wind River Linux][NUC_Lnx] |
| 模擬閘道            | C                    | [Linux][Sim_Lnx]、[Windows][Sim_Win] |

## <a name="extended-iot-scenarios-use-other-azure-services-and-tools"></a>延伸 IoT 案例︰使用其他 Azure 服務和工具

當您已將裝置連線到 IoT 中樞時，可以瀏覽使用其他 Azure 工具和服務的其他案例︰

| 案例                                    | Azure 服務或工具              |
|---------------------------------------------|------------------------------------|
| [管理 IoT 中樞訊息][Mg_IoT_Hub_Msg]                    | iothub-explorer 工具               |
| [管理 IoT 裝置][Mg_IoT_Dv]               | iothub-explorer 工具               |
| [將 IoT 中樞訊息儲存至 Azure 儲存體][Sv_IoT_Msg_Stor]                      | Azure 表格儲存體               |
| [將感應器資料視覺化][Vis_Data]             | Microsoft Power BI、Azure Web Apps |
| [使用感應器資料預測天氣][Weather_Forecast] | Azure Machine Learning             |
| [自動異常偵測和回應][Anomaly_Detect]    | Azure Logic Apps                   |

## <a name="next-steps"></a>後續步驟

當您完成這些教學課程時，可以在[開發人員指南][lnk-dev-guide]中進一步探索 IoT 中樞的功能。 您可以在[作法][lnk-how-to]一節找到其他教學課程。


[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[Sim_NET]: iot-hub-csharp-csharp-getstarted.md
[Sim_Jav]: iot-hub-java-java-getstarted.md
[Sim_Nd]: iot-hub-node-node-getstarted.md
[Sim_Pyth]: iot-hub-python-getstarted.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md
[Sim_Lnx]: iot-hub-linux-gateway-sdk-get-started.md
[Sim_Win]: iot-hub-windows-gateway-sdk-get-started.md
[Mg_IoT_Hub_Msg]: iot-hub-explorer-cloud-device-messaging.md
[Mg_IoT_Dv]: iot-hub-device-management-iothub-explorer.md
[Sv_IoT_Msg_Stor]: iot-hub-store-data-in-azure-table-storage.md
[Vis_Data]: iot-hub-live-data-visualization-in-power-bi.md
[Weather_Forecast]: iot-hub-weather-forecast-machine-learning.md
[Anomaly_Detect]: iot-hub-monitoring-notifications-with-azure-logic-apps.md
[lnk-dev-guide]: iot-hub-devguide.md
[lnk-how-to]: iot-hub-how-to.md
