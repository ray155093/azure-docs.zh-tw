<properties
    pageTitle="開始使用 IoT 中樞閘道 SDK | Microsoft Azure"
    description="使用 Windows 的 Azure IoT 中樞閘道 SDK 逐步解說，說明您使用 Azure IoT 中樞閘道 SDK 時應該了解的重要概念。"
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>



# <a name="iot-gateway-sdk-(beta)---get-started-using-windows"></a>IoT 閘道 SDK (Beta) - 開始使用 Windows

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>如何建置範例

開始之前，您必須[設定開發環境][lnk-setupdevbox]以在 Windows 上使用 SDK。

1. 開啟 [VS2015 的開發人員命令提示字元]  命令提示字元。
2. 瀏覽至 **azure-iot-gateway-sdk** 儲存機制本機複本中的根資料夾。
3. 執行 **tools\\build.cmd** 指令碼。 此指令碼會建立 Visual Studio 方案檔、建置方案，並執行測試。 您可以在 **azure-iot-gateway-sdk** 存放庫本機複本的 **build** 資料夾中找到此 Visual Studio 方案。

## <a name="how-to-run-the-sample"></a>如何執行範例

1. **build.cmd** 指令碼會在存放庫本機複本中建立稱為 **build** 的資料夾。 此資料夾包含在此範例中使用的兩個模組。

    build 指令碼會將 **logger_hl.dll** 放在 **build\\modules\\logger\\Debug** 資料夾中，並將 **hello_world_hl.dll** 放在 **build\\modules\\hello_world\\Debug** 資料夾中。 使用這些路徑作為「模組路徑」  值 (如下面的 JSON 設定檔案中所示)。

2. **samples\\hello_world\\src** 資料夾中的 **hello_world_win.json** 檔案是適用於 Windows 且可用來執行範例的範例 JSON 設定檔案。 下面顯示的範例 JSON 設定假設您已將閘道 SDK 儲存機制複製到 **C:** 磁碟機的根目錄。 如果您已將它下載到另一個位置，則需要據此調整 **samples\\hello_world\\src\\hello_world_win.json** 檔案中的 **module path** 值。

3. 針對 **logger_hl** 模組，在 **args** 區段中，將 **filename** 值設定為包含記錄資料的檔案名稱和路徑。

    這是適用於 Windows 且將 **log.txt** 檔案寫入 **C:** 磁碟機之根目錄的 JSON 設定檔案範例。

    ```
    {
      "modules" :
      [
        {
          "module name" : "logger_hl",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
          "args" : 
          {
            "filename":"C:\\log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\\\modules\\hello_world\\Debug\\hello_world_hl.dll",
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```

3. 在命令提示字元中，瀏覽至 **azure-iot-gateway-sdk** 儲存機制本機複本中的根資料夾。
4. 執行以下命令：
  
  ```
  build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
  ```

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md


<!--HONumber=Oct16_HO2-->


