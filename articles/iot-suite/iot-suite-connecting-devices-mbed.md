---
title: "在 mbed 上使用 C 連接裝置 | Microsoft Docs"
description: "描述如何在 mbed 上使用已寫入 C 的應用程式，將裝置連接至 Azure IoT Suite 預先設定遠端監視方案。"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 9551075e-dcf9-488f-943e-d0eb0e6260be
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: df9772796796f7383aafc583b01f299a53679d88
ms.openlocfilehash: 12535cbb6fa63c24dd63903380d697f8f38db6f9
ms.contentlocale: zh-tw
ms.lasthandoff: 02/27/2017


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-mbed"></a>將裝置連接至遠端監視預先設定方案 (mbed)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-c-sample-solution"></a>建置並執行 C 範例方案

下列指示描述將[啟用 mbed 的 Freescale FRDM-K64F][lnk-mbed-home] 裝置連接至遠端監視解決方案的步驟。

### <a name="connect-the-mbed-device-to-your-network-and-desktop-machine"></a>將 mbed 裝置連接到網路和桌上型電腦

1. 使用乙太網路纜線將 mbed 裝置連接到您的網路。 這是必要的步驟，因為範例應用程式需要透過網際網路存取。

1. 請參閱 [mbed 使用者入門][lnk-mbed-getstarted]來將您的 mbed 裝置連接至桌上型電腦。

1. 如果桌上型電腦執行的是 Windows，請參閱 [PC 組態][lnk-mbed-pcconnect]以設定對您 mbed 裝置的序列埠存取。

### <a name="create-an-mbed-project-and-import-the-sample-code"></a>建立 mbed 專案並匯入範例程式碼

依照下列步驟將一些範例程式碼新增至 mbed 專案。 匯入遠端監視入門專案，然後將專案變更為使用 MQTT 通訊協定 (而非 AMQP 通訊協定)。 目前，您必須使用 MQTT 通訊協定，才能使用 IoT 中樞的裝置管理功能。

1. 在您的 Web 瀏覽器中，移至 mbed.org [開發人員網站](https://developer.mbed.org/)。 如果您還沒有註冊，您會看到建立帳戶的選項 (它是免費的)。 否則，請使用您的帳戶認證登入。 然後按一下頁面右上角的 [編譯器]  。 這項動作會將您帶往 [工作區] 介面。

1. 請確定您使用的硬體平台出現在視窗的右上角，或按一下右手邊的圖示來選取您的硬體平台。

1. 在主功能表上按一下 [匯入]  。 然後按一下 [按一下這裡從 URL 匯入]。
   
    ![開始匯入至 mbed 工作區][6]

1. 在快顯視窗中，輸入範例程式碼 https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/ 連結，然後按一下 [匯入]。
   
    ![將程式碼範例匯入至 mbed 工作區][7]

1. 您可以看到匯入此專案的 mbed 編譯器視窗也匯入各種程式庫。 有些是由 Azure IoT 小組提供和維護 ([azureiot_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/)、[iothub_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/)、[iothub_amqp_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/)、[azure_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/))，其他則是可以在 mbed 程式庫目錄中取得的協力廠商程式庫。
   
    ![檢視 mbed 專案][8]

1. 在 [程式工作區] 中，以滑鼠右鍵按一下 **iothub\_amqp\_transport** 程式庫，按一下 [刪除]，然後按一下 [確定] 進行確認。

1. 在 [程式工作區] 中，以滑鼠右鍵按一下 **azure\_amqp\_c** 程式庫，按一下 [刪除]，然後按一下 [確定] 進行確認。

1. 以滑鼠右鍵按一下 [程式工作區] 中的 **remote_monitoring** 專案，選取 [匯入程式庫]，然後選取 [從 URL]。
   
    ![開始將程式庫匯入至 mbed 工作區][6]

1. 在快顯視窗中，輸入 MQTT 傳輸程式庫 https://developer.mbed.org/users/AzureIoTClient/code/iothub\_mqtt\_transport/ 連結，然後按一下 [匯入]。
   
    ![將程式庫匯入至 mbed 工作區][12]

1. 重複上述步驟以從 https://developer.mbed.org/users/AzureIoTClient/code/azure\_umqtt\_c/ 新增 MQTT 程式庫。

1. 您的工作區現在如下所示：

    ![檢視 mbed 工作區][13]

1. 開啟 remote\_monitoring\remote_monitoring.c 檔案，並以下列程式碼取代現有的 `#include` 陳述字：

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"

    #ifdef MBED_BUILD_TIMESTAMP
    #include "certs.h"
    #endif // MBED_BUILD_TIMESTAMP
    ```
1. 刪除 remote\_monitoring\remote\_monitoring.c 檔案中的所有其餘程式碼。

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>建置並執行範例

新增程式碼來叫用 **remote\_monitoring\_run** 函式，然後建置並執行裝置應用程式。

1. 以位於 remote\_monitoring.c 檔案結尾的下列程式碼新增 **main** 函式，以叫用 **remote\_monitoring\_run** 函式︰
   
    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. 按一下 [編譯]  來建置程式。 您可以安全地略過任何警告，但如果建置產生錯誤，請修正錯誤後再繼續。

1. 如果建置成功，mbed 編譯器網站會產生含有您專案名稱的 .bin 檔案，並將它下載到您的本機電腦。 將 .bin 檔案複製到裝置。 如果將 .bin 檔案儲存到裝置，將會導致裝置重新啟動並執行包含在 .bin 檔案中的程式。 您可以按 mbed 裝置上的 [重設] 按鈕來隨時手動重新啟動程式。

1. 使用 SSH 用戶端應用程式 (例如 PuTTY) 連接至裝置。 您可以查看 [Windows 裝置管理員] 來確定裝置使用的序列埠。
   
    ![][11]

1. 在 PuTTY 中，按一下 [序列]  連接類型。 裝置通常的連線傳輸速率是 9600，因此請在 [速度]  方塊中輸入 9600。 然後按一下 [開啟] 。

1. 程式開始執行。 連接時如果程式沒有自動啟動，您可能必須重設面板 (按 CTRL + Break 或按面板的重設按鈕)。
   
    ![][10]

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png
[12]: ./media/iot-suite-connecting-devices-mbed/mbed7.png
[13]: ./media/iot-suite-connecting-devices-mbed/mbed8.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration

