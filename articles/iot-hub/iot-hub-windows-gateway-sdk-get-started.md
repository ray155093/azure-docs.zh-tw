---
title: "開始使用 Azure IoT Edge (Windows) | Microsoft Docs"
description: "如何在 Windows 機器上建置 Azure IoT Edge 閘道，並了解 Azure IoT Edge 中的重要概念，例如模組和 JSON 組態檔。"
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
ms.date: 03/28/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 2a46a3511d4d286c26aa9ca2e4e619414d82be1d
ms.contentlocale: zh-tw
ms.lasthandoff: 05/16/2017


---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>在 Windows 上探索 Azure IoT Edge 架構

[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>如何建置範例

開始之前，您必須[設定開發環境][lnk-setupdevbox]以在 Windows 上使用 SDK。

1. 開啟「VS 2015 開發人員命令提示字元」或「VS 2017 開發人員命令提示字元」命令提示字元。
1. 瀏覽至 **iot-edge** 存放庫的本機複本中的根資料夾。
1. 執行 **tools\\build.cmd** 指令碼。 此指令碼會建立 Visual Studio 方案檔及建置方案。 您可以在 **iot-edge** 存放庫本機複本的 **build** 資料夾中找到此 Visual Studio 方案。 您可以為指令碼指定其他參數，以建置並執行單元測試和端對端測試。 這些參數分別是 **--run-unittests** 和 **--run-e2e-tests**。

## <a name="how-to-run-the-sample"></a>如何執行範例

1. **build.cmd** 指令碼會在存放庫本機複本中建立稱為 **build** 的資料夾。 此資料夾包含在此範例中使用的兩個 IoT Edge 模組。

    build 指令碼會將 **logger.dll** 放在 **build\\modules\\logger\\Debug** 資料夾中，並將 **hello\_world.dll** 放在 **build\\modules\\hello_world\\Debug** 資料夾中。 使用這些路徑作為**模組路徑**值，如下列 JSON 設定檔所示。
1. hello\_world\_sample 程序會採用 JSON 組態檔的路徑作為命令列引數。 下列範例 JSON 檔案提供於 SDK 存放庫中 (位於 **samples\\hello\_world\\src\\hello\_world\_win.json**)。 除非您修改 build 指令碼以將模組或範例可執行檔放置在非預設位置，否則此組態檔將保有原始功能。

   > [!NOTE]
   > 模組路徑是相對於 hello\_world\_sample.exe 所在的目錄。 範例 JSON 組態檔的預設值為在目前的工作目錄中寫入 'log.txt'。

    ```json
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

1. 瀏覽至 **iot-edge** 存放庫本機複本的根資料夾。

1. 執行以下命令：

   `build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json`

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/iot-edge/blob/master/doc/devbox_setup.md

