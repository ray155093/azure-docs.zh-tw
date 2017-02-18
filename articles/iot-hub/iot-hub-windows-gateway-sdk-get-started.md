---
title: "開始使用 Azure IoT 閘道 SDK (Windows) | Microsoft Docs"
description: "如何在 Windows 電腦上建置閘道閘，並了解 Azure IoT 閘道 SDK 中的重要概念，例如模組和 JSON 組態檔。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 9aff3724-5e4e-40ec-b95a-d00df4f590c5
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: e1cf5ed3f2434a9e98027afd0225207ad5d2f1b1
ms.openlocfilehash: 3d9997655e19ba800bf3462d5ebd3f7c7210271f


---
# <a name="get-started-with-the-azure-iot-gateway-sdk-windows"></a>開始使用 Azure IoT 閘道 SDK (Windows)
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>如何建置範例
開始之前，您必須[設定開發環境][lnk-setupdevbox]以在 Windows 上使用 SDK。

1. 開啟 [VS2015 的開發人員命令提示字元]  命令提示字元。
2. 瀏覽至 **azure-iot-gateway-sdk** 儲存機制本機複本中的根資料夾。
3. 執行 **tools\\build.cmd** 指令碼。 此指令碼會建立 Visual Studio 方案檔及建置方案。 您可以在 **azure-iot-gateway-sdk** 存放庫本機複本的 **build** 資料夾中找到此 Visual Studio 方案。 您可以為指令碼指定其他參數，以建置並執行單元測試和端對端測試。 這些參數分別是 **--run-unittests** 和 **--run-e2e-tests**。 

## <a name="how-to-run-the-sample"></a>如何執行範例
1. **build.cmd** 指令碼會在存放庫本機複本中建立稱為 **build** 的資料夾。 此資料夾包含在此範例中使用的兩個模組。
   
    build 指令碼會將 **logger.dll** 放在 **build\\modules\\logger\\Debug** 資料夾中，並將 **hello_world.dll** 放在 **build\\modules\\hello_world\\Debug** 資料夾中。 使用這些路徑作為**模組路徑**值 (如下列 JSON 設定檔所示)。
2. Hello_world_sample 程序會採用 JSON 組態檔的路徑，並做為命令列的引數。 我們已在 **azure-iot-gateway-sdk\samples\hello_world\src\hello_world_win.json** 以部分儲存機制的形式提供下列範例 JSON 檔。 除非您修改 build 指令碼以將模組或範例可執行檔放置在非預設位置，否則它將保有原始功能。 

   > [!NOTE]
   > 模組路徑是相對於 hello_world_sample.exe 所在的目錄。 範例 JSON 組態檔的預設值為在目前的工作目錄中寫入 'log.txt'。
   
    ```
    {
      "modules": [
        {
          "name": "logger",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
            }
          },
          "args": { "filename": "log.txt" }
        },
        {
          "name": "hello_world",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "..\\..\\..\\modules\\hello_world\\Debug\\hello_world.dll"
            }
          },
          "args": null
          }
      ],
      "links": [
        {
          "source": "hello_world",
          "sink": "logger"
        }
      ]
    }
    ```
3. 瀏覽至 **azure-iot-gateway-sdk** 儲存機制本機複本的根資料夾。

4. 執行以下命令：
   
   ```
   build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
   ```

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md



<!--HONumber=Jan17_HO3-->


