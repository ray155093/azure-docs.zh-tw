---
title: "開始使用 Azure IoT 閘道 SDK (Linux) | Microsoft Docs"
description: "如何在 Linux 機器上建置閘道，並了解 Azure IoT 閘道 SDK 中的重要概念，例如模組和 JSON 組態檔。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: cf537bdd-2352-4bb1-96cd-a283fcd3d6cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/23/2016
ms.author: andbuc
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 37b2a82d7f6043224e68219fde753eef73078ffd
ms.openlocfilehash: b3cc8e53b0c8bb7ea40b6ebcebe1f97d4a3e1180
ms.lasthandoff: 03/02/2017


---
# <a name="explore-the-iot-gateway-sdk-architecture-on-linux"></a>在 Linux 上探索 IoT 閘道 SDK 架構
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>如何建置範例
開始之前，您必須[設定開發環境][lnk-setupdevbox]以在 Linux 上使用 SDK。

1. 開啟殼層。
2. 瀏覽至 **azure-iot-gateway-sdk** 儲存機制本機複本中的根資料夾。
3. 執行 **tools/build.sh** 指令碼。 此指令碼使用 **cmake** 公用程式，在 **azure-iot-gateway-sdk** 存放庫本機複本的根資料夾中建立稱為 **build** 的資料夾，以及產生 Makefile。 此指令碼接著會建置方案，略過單元測試和端對端測試。 如果您想要建置並執行單元測試，請新增 **--run-unittests** 參數。 如果您想要建置並執行端對端測試，請新增 **--run-e2e-tests** 參數。

> [!NOTE]
> 每次執行 **build.sh** 指令碼時，都會先在 **azure-iot-gateway-sdk** 存放庫本機複本的根資料夾中刪除再重新建立 **build** 資料夾。
> 
> 

## <a name="how-to-run-the-sample"></a>如何執行範例
1. **build.sh** 指令碼會在 **azure-iot-gateway-sdk** 儲存機制本機複本的 **build** 資料夾中產生其輸出。 這包含在此範例中使用的兩個模組。
   
    build 指令碼會將 **liblogger.so** 放在 **build/modules/logger/** 資料夾中，並將 **libhello_world.so** 放在 **build/modules/hello_world/** 資料夾中。 使用這些路徑作為「模組路徑」  值 (如下面的 JSON 設定檔案中所示)。
2. Hello_world_sample 程序會採用 JSON 組態檔的路徑，並做為命令列的引數。 我們已在 **azure-iot-gateway-sdk/samples/hello_world/src/hello_world_win.json** 以部分儲存機制的形式提供範例 JSON，其複製如下。 除非您修改 build 指令碼以將模組或範例可執行檔放置在非預設位置，否則它將保有原始功能。

   > [!NOTE]
   > 模組路徑是 hello_world_sample 可執行檔啟動之目前工作目錄的相對路徑，不是可執行檔所在的目錄。 範例 JSON 組態檔的預設值為在目前的工作目錄中寫入 'log.txt'。
   
    ```
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
3. 瀏覽至 **azure-iot-gateway-sdk/build** 資料夾。
4. 執行以下命令：
   
   ```
   ./samples/hello_world/hello_world_sample ./../samples/hello_world/src/hello_world_lin.json
   ``` 

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md

