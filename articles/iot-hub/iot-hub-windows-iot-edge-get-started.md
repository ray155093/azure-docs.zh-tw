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
ms.date: 06/07/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 5db39bab8e31a8e7026b34e72b4614b0f6f57772
ms.contentlocale: zh-tw
ms.lasthandoff: 06/09/2017


---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>在 Windows 上探索 Azure IoT Edge 架構

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="how-to-run-the-sample"></a>如何執行範例

**build.cmd** 指令碼會在 **iot-edge** 存放庫本機複本的 **build** 資料夾中產生其輸出。 這輸出包含在此範例中使用的兩個 IoT Edge 模組。

build 指令碼會將 **logger.dll** 放在 **build\\modules\\logger\\Debug** 資料夾中，並將 **hello\_world.dll** 放在 **build\\modules\\hello_world\\Debug** 資料夾中。 使用這些路徑作為**模組路徑**值，如下列 JSON 設定檔所示。

hello\_world\_sample 程序會採用 JSON 組態檔的路徑作為命令列引數。 下列範例 JSON 檔案提供於 SDK 存放庫中 (位於 **samples\\hello\_world\\src\\hello\_world\_win.json**)。 除非您修改 build 指令碼以將 IoT Edge 模組或範例可執行檔放置在非預設位置，否則此組態檔將保有原始功能。

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

1. 瀏覽至 **iot-edge** 存放庫的本機複本根中的 **build** 資料夾。

1. 執行以下命令：

    ```cmd
    samples\hello_world\Debug\hello_world_sample.exe ..\samples\hello_world\src\hello_world_win.json
    ```

[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]

