---
title: "開始使用 Azure IoT Edge (Linux) | Microsoft Docs"
description: "如何在 Linux 機器上建置閘道，並了解 Azure IoT Edge 中的重要概念，例如模組和 JSON 組態檔。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: cf537bdd-2352-4bb1-96cd-a283fcd3d6cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 38fd72fda6ea5d03d72c950803b09dd0b9e34fe4
ms.contentlocale: zh-tw
ms.lasthandoff: 05/18/2017


---
# <a name="explore-azure-iot-edge-architecture-on-linux"></a>在 Linux 上探索 Azure IoT Edge 架構

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>如何建置範例

開始之前，您必須[設定開發環境][lnk-setupdevbox]以在 Linux 上使用 SDK。

1. 開啟殼層。
1. 瀏覽至 **iot-edge** 存放庫的本機複本中的根資料夾。
1. 執行 **tools/build.sh** 指令碼。 此指令碼使用 **cmake** 公用程式，在 **iot-edge** 存放庫本機複本的根資料夾中建立稱為 **build** 的資料夾，以及產生 Makefile。 此指令碼接著會建置方案，略過單元測試和端對端測試。 如果您想要建置並執行單元測試，請新增 **--run-unittests** 參數。 如果您想要建置並執行端對端測試，請新增 **--run-e2e-tests** 參數。

> [!NOTE]
> 每次執行 **build.sh** 指令碼時，都會先在 **iot-edge** 存放庫本機複本的根資料夾中刪除再重新建立 **build** 資料夾。

## <a name="how-to-run-the-sample"></a>如何執行範例

1. **build.sh** 指令碼會在 **iot-edge** 存放庫本機複本的 **build** 資料夾中產生其輸出。 這輸出包含在此範例中使用的兩個 IoT Edge 模組。

    build 指令碼會將 **liblogger.so** 放在 **build/modules/logger/** 資料夾中，並將 **libhello\_world.so** 放在 **build/modules/hello_world/** 資料夾中。 使用這些路徑作為**模組路徑**值 (如下列範例 JSON 設定檔所示)。
1. hello\_world\_sample 程序會採用 JSON 組態檔的路徑做為命令列引數。 下列範例 JSON 檔案提供於 SDK 儲存裝置中 (位於 **samples/hello\_world/src/hello\_world\_lin.json**)。 除非您修改 build 指令碼以將 IoT Edge 模組或範例可執行檔放置在非預設位置，否則此組態檔將保有原始功能。

   > [!NOTE]
   > 模組路徑是 hello\_world\_sample 可執行檔啟動之目前工作目錄的相對路徑，不是可執行檔所在的目錄。 範例 JSON 組態檔的預設值為在目前的工作目錄中寫入 'log.txt'。

    ```json
    {
        "modules" :
        [
            {
              "name" : "logger",
              "loader": {
                "name": "native",
                "entrypoint": {
                  "module.path": "./modules/logger/liblogger.so"
                }
              },
              "args" : {"filename":"log.txt"}
            },
            {
                "name" : "hello_world",
              "loader": {
                "name": "native",
                "entrypoint": {
                  "module.path": "./modules/hello_world/libhello_world.so"
                }
              },
                "args" : null
            }
        ],
        "links":
        [
            {
                "source": "hello_world",
                "sink": "logger"
            }
        ]
    }
    ```
1. 瀏覽至 **build** 資料夾。
1. 執行以下命令：

   `./samples/hello_world/hello_world_sample ./../samples/hello_world/src/hello_world_lin.json`

[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/iot-edge/blob/master/doc/devbox_setup.md

