---
title: "Azure IoT 中樞快速入門 | Microsoft Docs"
description: "如何開始使用 IoT 中樞服務"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: d7360c3d41a0e4cf0d182e510d6bc2fccd915dd1
ms.openlocfilehash: 51ac0c0d3a91070fc8f5b3892409af838e91068e
ms.lasthandoff: 03/01/2017


---
# <a name="get-started-with-azure-iot-hub-or-azure-iot-gateway-sdk"></a>開始使用 Azure IoT 中樞或 Azure IoT 閘道 SDK

您可以選擇數個教學課程之一，以便開始使用 IoT 中樞服務或閘道 SDK。

## <a name="iot-hub"></a>IoT 中樞

Azure IoT 中樞是一項完全受管理的服務，可在數百萬個物聯網 (IoT) 裝置和一個解決方案後端之間啟用可靠且安全的雙向通訊。

若要開始使用 IoT 中樞服務，您可以：

- 遵循使用在開發電腦上執行之模擬裝置的教學課程。 選擇使用您慣用程式設計語言的快速入門教學課程︰[.NET][lnk-dotnet]、[Java][lnk-java] 或 [Node.js][lnk-nodejs]。

- 遵循使用實體裝置的教學課程。 選擇使用您慣用硬體平台的快速入門教學課程︰[Raspberry Pi][lnk-rasp-pi]、[Intel Edison][lnk-edison] 或 [Arduino][lnk-arduino]。 這些教學課程包含有關如何取得硬體裝置的資訊。

- 若想了解如何使用 C 語言來開發 IoT 裝置，請參閱 [C 適用的 Azure IoT 裝置 SDK 簡介] [lnk-c-intro]一文。

## <a name="gateway-sdk"></a>閘道器 SDK

您可以使用閘道 SDK 來建置自訂現場閘道。 閘道可以執行一些工作，例如執行分析、進行可降低延遲的時效性決策、提供裝置管理服務、強制執行安全性和隱私權條件約束，以及執行通訊協定轉譯。

若要開始使用閘道 SDK，您可以：

- 遵循使用在開發電腦上執行之模擬閘道的教學課程。 您可以選擇適用於 [Linux] [lnk-linux] 或 [Windows][lnk-windows] 的快速入門教學課程。

- 遵循使用實體裝置的教學課程。 您可以選擇一個快速入門教學課程，而該教學課程會使用[具有 Intel NUC (Next Unit of Computing) 的模擬裝置][lnk-gateway-sim] 或使用[具有 Intel NUC 的 SensorTag 裝置][lnk-gateway-tag]。

## <a name="next-steps"></a>後續步驟

當您完成快速入門教學課程時，您可以在[開發人員指南][lnk-devguide]和[做法] [lnk-howto]教學課程中探索更多 IoT 中樞與閘道 SDK 功能。

[lnk-dotnet]: ./iot-hub-csharp-csharp-getstarted.md
[lnk-java]: ./iot-hub-java-java-getstarted.md
[lnk-nodejs]: ./iot-hub-node-node-getstarted.md
[lnk-c-intro]: ./iot-hub-device-sdk-c-intro.md
[lnk-rasp-pi]: ./iot-hub-raspberry-pi-kit-node-get-started.md
[lnk-edison]: ./iot-hub-intel-edison-kit-node-get-started.md
[lnk-arduino]: ./iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[lnk-linux]: ./iot-hub-linux-gateway-sdk-get-started.md
[lnk-windows]: ./iot-hub-windows-gateway-sdk-get-started.md
[lnk-gateway-sim]: ./iot-hub-gateway-kit-c-sim-get-started.md
[lnk-gateway-tag]: ./iot-hub-gateway-kit-c-get-started.md
[lnk-devguide]: ./iot-hub-devguide.md
[lnk-howto]: ./iot-hub-how-to.md

