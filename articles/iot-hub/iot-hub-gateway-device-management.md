<properties
 pageTitle="啟用 IoT 閘道的受管理裝置 | Microsoft Azure"
 description="使用利用閘道 SDK 以及 IoT 中樞所管理裝置所建立之 IoT 閘道的指引主題。"
 services="iot-hub"
 documentationCenter=""
 authors="chipalost"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="cstreet"/>
 
# 啟用 IoT 閘道的受管理裝置

## 基本裝置隔離

組織通常會使用 IoT 閘道來提升其 IoT 方案的整體安全性。某些裝置需要將資料傳送至雲端，但無法保護本身免於網際網路上的威脅。您可以讓外部執行緒看不到這些裝置，方法是讓它們透過閘道與外界通訊。

閘道位於安全環境與開放式網際網路之間的邊界。裝置會與閘道通訊，而且閘道會將訊息傳遞至正確的雲端目的地。閘道是針對外部執行緒進行強化、封鎖未經授權的要求、允許授權的輸入流量，以及將該輸入流量轉送至正確裝置。

![][1]

您也可以透過閘道保護裝置，以獲得額外一層安全性。在此情況下，您只需要針對最新漏洞修補閘道 OS，而不是更新每個裝置上的 OS。

## 隔離加上智慧

若只要隔離裝置，則強化的路由器就已足夠。不過，IoT 方案通常需要閘道所提供的智慧不只是隔離裝置。例如，您可能想要從雲端管理您的裝置。您可以針對方案的雲端管理組件使用 LWM2M (標準裝置管理通訊協定)。不過，裝置會使用非啟用 TCP/IP 功能的通訊協定來傳送遙測。此外，裝置會產生大量資料，而且您只想要上傳篩選過的遙測子集。您可以建置方案，其中包含可處理兩個不同資料串流的 IoT 閘道。閘道應該：

-   了解「遙測」，並對其進行篩選，然後透過閘道將它上傳至雲端。閘道不再只是只在裝置與雲端之間轉送資料的路由器。

-   只會在裝置與雲端之間交換「LWM2M 裝置管理資料」。閘道不需要了解傳入的資料，而且只需要確定在裝置與雲端之間來回傳遞資料。

下圖說明這個案例：

![][2]

## 方案︰ Azure IoT 裝置管理和閘道 SDK 

公用預覽版本的 [Azure IoT 裝置管理][lnk-device-management]和 Beta 版本的 [Azure IoT 閘道 SDK] 會啟用此案例。閘道會處理每個資料串流，如下所示︰

-   **遙測**︰您可以使用閘道 SDK 來建置管線，以了解、篩選並傳送遙測資料至雲端。閘道 SDK 提供程式碼來代表開發人員實作這個管線的各個組件。您可以在 [IoT 閘道 SDK - 入門][lnk-gateway-get-started]教學課程中找到 SDK 架構的詳細資訊。

-   **裝置管理**：Azure 裝置管理提供在裝置上執行的 LWM2M 用戶端，以及向裝置發出管理命令的雲端介面。
    
    您不需要閘道上的任何特殊邏輯，因為它不需要處理裝置與您 IoT 中樞之間交換的 LWM2M 資料。您可以在閘道上啟用網際網路連線共用 (許多現代作業系統的一項功能)，以啟用 LWM2M 資料的交換。您可以選擇此案例的適當作業系統，因為閘道 SDK 支援各種作業系統。以下是在 [Windows 10] 和 [Ubuntu] \(多個支援的作業系統中的其中兩個) 上啟用網際網路連線共用上的指示。

下圖顯示用來使用 [Azure IoT 裝置管理][lnk-device-management]和 [Azure IoT 閘道 SDK] 啟用此案例的高階架構。

![][3]

## 後續步驟

若要了解如何使用閘道 SDK，請參閱下列教學課程︰

- [IoT 閘道 SDK - 開始使用 Linux][lnk-gateway-get-started]
- [IoT 閘道 SDK – 搭配使用模擬裝置與 Linux 來傳送裝置到雲端訊息][lnk-gateway-simulated]

若要在深入了解如何使用 IoT 中樞管理裝置，請參閱 [Azure IoT 中樞裝置管理用戶端程式庫簡介][lnk-library-c]。

若要進一步探索 IoT 中樞的功能，請參閱︰

- [設計您的解決方案][lnk-design]
- [開發人員指南][lnk-devguide]
- [使用閘道 SDK 模擬裝置][lnk-gateway]
- [使用 Azure 入口網站管理 IoT 中樞][lnk-portal]

<!-- Images and links -->
[1]: media/iot-hub-gateway-device-management/overview.png
[2]: media/iot-hub-gateway-device-management/manage.png
[Azure IoT 閘道 SDK]: https://github.com/Azure/azure-iot-gateway-sdk/
[Windows 10]: http://windows.microsoft.com/zh-TW/windows/using-internet-connection-sharing#1TC=windows-7
[Ubuntu]: https://help.ubuntu.com/community/Internet/ConnectionSharing
[3]: media/iot-hub-gateway-device-management/manage_2.png
[lnk-gateway-get-started]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-gateway-simulated]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->