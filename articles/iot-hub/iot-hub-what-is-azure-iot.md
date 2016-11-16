---
title: "適用於物聯網的 Azure 解決方案 | Microsoft Docs"
description: "Azure 上的 IoT 概觀，包括架構解決方案範例，以及它如何與 Azure IoT 中樞、裝置 SDK 以及預先設定的解決方案相關聯"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a859e379-dca7-42fa-bdf6-1125c86ad140
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2dbf639abfa505eb329769bcc346efb5f1db443e


---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>後續步驟
Azure IoT 中樞是一項 Azure 服務，可讓應用程式後端與數百萬個裝置進行安全可靠的雙向通訊。 它可讓應用程式後端︰

* 從您的裝置接收大規模遙測。
* 將資料從您的裝置路由傳送到串流事件處理器。
* 從裝置接收檔案上傳。
* 將雲端到裝置的命令傳送到特定裝置。

您可以使用 IoT 中樞來實作自己的方案後端。 此外，IoT 中樞還包含裝置身分識別登錄，可供用來佈建裝置、其安全性認證和其連線到中樞的權限。 若要深入了解 IoT 中樞，請參閱[何謂 IoT 中樞？][lnk-iot-hub]。

若要了解 Azure IoT 中樞如何啟用標準型裝置管理，以便您遠端管理、設定和更新您的裝置，請參閱 [IoT 中樞的裝置管理概觀][lnk-device-management]。

您可以使用 IoT 裝置 SDK 來實作用戶端應用程式，以便在各式各樣的裝置硬體平台和作業系統上執行。 IoT 裝置 SDK 包含程式庫，可協助將遙測傳送至 IoT 中樞，並接收雲端到裝置的命令。 當您使用 SDK 時，您可以從數種網路通訊協定中選擇，以便與 IoT 中樞通訊。 若要深入了解，請參閱[裝置 SDK 的相關資訊][lnk-device-sdks]。

若要開始撰寫一些程式碼和執行一些範例，請參閱[開始使用 IoT 中樞][lnk-getstarted]教學課程。

您也可能會對 [Azure IoT 套件][lnk-iot-suite]有興趣，這是一套預先設定的方案。 IoT 套件可讓您快速入門和調整 IoT 專案，以因應常見的 IoT 案例，例如遠端監視、資產管理和預測性維護。

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md



<!--HONumber=Nov16_HO2-->


