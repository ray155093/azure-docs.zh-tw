---
title: "模擬 Raspberry Pi 至 cloud (Node.js) - 將 Raspberry Pi Web 模擬器連線至 Azure IoT 中樞 | Microsoft Docs"
description: "了解如何設定及連線模擬的 Raspberry Pi 和 Azure IoT 中樞，以將資料傳送到 Azure 雲端平台。 本教學課程不需要實體面板。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "raspberry pi 模擬器, azure iot raspberry pi, raspberry pi iot 中樞, raspberry pi 將資料傳送至雲端, raspberry pi 至 cloud"
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/7/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: b76c64b677a1a57a032cb71d2922778e7cf700c8
ms.contentlocale: zh-tw
ms.lasthandoff: 07/14/2017

---

# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>將 Raspberry Pi 線上模擬器連線至 Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

在本教學課程中，您首先會了解使用 Raspberry Pi 線上模擬器的基本知識。 接著會了解如何使用 [Azure IoT 中樞](iot-hub-what-is-iot-hub.md)順暢地將 Pi 模擬器連線至雲端。 

如果您有實體裝置，請瀏覽[將 Raspberry Pi 連線至 Azure IoT 中樞](iot-hub-raspberry-pi-kit-node-get-started.md)開始著手。 

## <a name="what-you-do"></a>您要做什麼

* 了解 Raspberry Pi 線上模擬器的基本知識。
* 建立 IoT 中樞。
* 在 IoT 中樞對於 Pi 註冊裝置。
* 在 Pi 上執行範例應用程式，將模擬感應器資料傳送至 IoT 中樞。

將模擬 Raspberry Pi 連線至您建立的 IoT 中樞。 然後使用模擬器執行範例應用程式，以產生感應器資料。 最後，將感應器資料傳送至 IoT 中樞。

## <a name="what-you-learn"></a>您學到什麼

* 如何建立 Azure IoT 中樞，並取得新的裝置連接字串。 如果您沒有 Azure 帳戶，請花幾分鐘的時間[建立免費的 Azure 試用帳戶](https://azure.microsoft.com/free/)。
* 如何使用 Raspberry Pi 線上模擬器。
* 如何將感應器資料傳送至 IoT 中樞。

## <a name="overview-of-raspberry-pi-web-simulator"></a>Raspberry Pi Web 模擬器概觀

按一下按鈕以啟動 Raspberry Pi 線上模擬器。

> [!div class="button"]
[啟動 Raspberry Pi 模擬器](https://azure-samples.github.io/raspberry-pi-web-simulator/)

Web 模擬器中有三個區域。
* 組件區域 - 預設線路是 Pi 與 BME280 感應器和 LED 連線。 此區域已在預覽版本中鎖定，所以您目前無法進行自訂。
* 編碼區域 - 可供您使用 Raspberry Pi 編碼的線上程式碼編輯器。 預設範例應用程式有助於從 BME280 感應器收集感應器資料，並傳送至您的 Azure IoT 中樞。 應用程式與實際 Pi 裝置完全相容。 
* 整合式主控台視窗 - 它會顯示您的程式碼輸出。 在這個視窗頂端，有三個按鈕。
   * **執行** - 在編碼區域中執行應用程式。
   * **重設** - 將編碼區域重設為預設範例應用程式。
   * **摺疊/展開** - 右邊有一個可供您摺疊/展開主控台視窗的按鈕。

> [!NOTE] 
預覽版本現在提供 Raspberry Pi Web 模擬器。 我們想要在 [Gitter 聊天室](https://gitter.im/Microsoft/raspberry-pi-web-simulator)中傾聽您的心聲。 原始程式碼公開於 [GitHub](https://github.com/Azure-Samples/raspberry-pi-web-simulator)。

![Pi 線上模擬器概觀](media/iot-hub-raspberry-pi-web-simulator/0_overview.png)

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]


## <a name="run-a-sample-application-on-pi-web-simulator"></a>在 Pi Web 模擬器上執行範例應用程式

1. 在編碼區域中，確定您是使用預設範例應用程式。 以 Azure IoT 中樞裝置連接字串取代行 15 中的預留位置。
   ![取代裝置連接字串](media/iot-hub-raspberry-pi-web-simulator/1_connectionstring.png)

2. 按一下 [執行] 或輸入 `npm start` 以執行應用程式。


您應該會看見下列輸出，顯示傳送至 IoT 中樞的感應器資料和訊息 ![輸出 - 從 Raspberry Pi 傳送至 IoT 中樞的感應器資料](media/iot-hub-raspberry-pi-web-simulator/2_run_application.png)


## <a name="next-steps"></a>後續步驟

您已執行範例應用程式收集感應器資料並傳送至 IoT 中樞。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

