---
title: 適用於 IT 專業人員的 Azure IoT 中樞資訊 | Microsoft Docs
description: 有助於 IT 專業人員使用 Azure IoT 中樞的資訊，例如連接埠需求和安全性背景。
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2016
ms.author: dobett

---
# 設定和管理 IoT 中樞的存取權
本文章提供的資訊可協助 IT 專業人員設定環境，讓 IoT 裝置可透過網路基礎結構與 IoT 中樞通訊。

## 網路連線
裝置可以在 Azure 中使用各種通訊協定來與 IoT 中樞通訊。一般而言，選擇的通訊協定是根據方案的特定需求而定。下表列出必須要為裝置開啟的輸出連接埠，以使用特定的通訊協定：

| 通訊協定 | 連接埠 |
| --- | --- |
| MQTT |8883 |
| AMQP |5671 |
| 透過 WebSockets 的 AMQP |443 |
| HTTPS |443 |
| LWM2M (裝置管理) |5684 |

當您在 Azure 區域中建立 IoT 中樞後，中樞將在該中樞的存留期間保留相同的 IP 位址。不過，為了維護服務品質，如果 Microsoft 將 IoT 中樞移至不同的縮放單位，則它會獲派新的 IP 位址。

## IoT 中樞和安全性
只有向 IoT 中樞註冊的裝置可與該 IoT 中樞進行通訊。已註冊的裝置必須獲得 *DeviceConnect* 權限。裝置會在它發出的每個要求中加入封裝了裝置唯一識別碼的權杖，藉此識別它自己。然後中樞會檢查該權杖的有效性，以及裝置是否在拒絕 (*DeviceConnect* 權限被撤銷) 的清單上。如需 IoT 中樞所支援權杖的相關資訊，請參閱[使用 IoT 中樞安全性權杖和 X.509 憑證][lnk-tokens]

IoT 中樞內對其他管理端點的存取權，也是透過一組權限來控制：*iothubowner*、*service*、*registryRead* 和 *registryReadWrite*。連接到 IoT 中樞的所有用戶端管理應用程式，都必須包含具有適當權限的權杖。

## 後續步驟
這篇文章包含特定資訊，供設定開發和測試環境的 IT 專業人員和開發人員閱讀。[Azure IoT 中樞開發人員指南中的「安全性」章節][lnk-devguide]提供 IoT 中樞內權杖和權限系統的其他資訊。

若要進一步探索 IoT 中樞的功能，請參閱︰

* [設計您的解決方案][lnk-design]
* [開發人員指南][lnk-devguide]
* [使用範例 UI 探索裝置管理][lnk-dmui]
* [使用閘道 SDK 模擬裝置][lnk-gateway]

[lnk-devguide]: iot-hub-devguide.md#security

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-tokens]: iot-hub-sas-tokens.md

<!----HONumber=AcomDC_0907_2016-->