---
title: "建立您的第一個 Azure IoT 閘道器模組 | Microsoft Docs"
description: "建立模組，並將它新增至範例應用程式以自訂模組行為。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: cd7660f4-7b8b-4091-8d71-bb8723165b0b
ms.service: iot-hub
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5e28422158684c3aaf0ac3fdf5b19c80fbccfb02
ms.contentlocale: zh-tw
ms.lasthandoff: 07/06/2017

---
<a id="lesson-5-create-your-first-azure-iot-gateway-module" class="xliff"></a>

# 第 5 課：建立您的第一個 Azure IoT 閘道器模組
雖然 Azure IoT Edge 可讓您建置以 Java、.NET 或 Node.js 撰寫的模組，但本教學課程會逐步引導您完成以 C 建置模組的步驟。

<a id="what-you-will-do" class="xliff"></a>

## 將執行的作業

- 在 Intel NUC 上編譯和執行 hello_world 範例應用程式。
- 建立模組，並在 Intel NUC 上編譯。
- 將新的模組新增至 hello_world 範例應用程式，然後在 Intel NUC 上執行範例。 新的模組會印出 "hello_world" 訊息加上時間戳記。

<a id="what-you-will-learn" class="xliff"></a>

## 學習目標

- 如何在 Intel NUC 上編譯和執行範例應用程式。
- 如何建立模組。
- 如何將模組新增至範例應用程式。

<a id="what-you-need" class="xliff"></a>

## 您需要什麼

已安裝在主機電腦上的 Azure IoT Edge。

<a id="folder-structure" class="xliff"></a>

## 資料夾結構

您在第 1 課複製的範例程式碼的第 5 課子資料夾中，有一個 `module` 資料夾和 `sample` 資料夾。

![my_module](media/iot-hub-gateway-kit-lessons/lesson5/my_module.png)

- `module/my_module` 資料夾包含用來建置模組的原始程式碼和指令碼。
- `sample` 資料夾包含用來建置範例應用程式的原始程式碼和指令碼。

<a id="compile-and-run-the-helloworld-sample-app-on-intel-nuc" class="xliff"></a>

## 在 Intel NUC 上編譯和執行 hello_world 範例應用程式

`hello_world` 範例會根據 `hello_world.json` 檔案建立閘道，此檔案中指定應用程式相關聯的兩個預先定義模組。 閘道器每隔 5 秒會將 "hello world" 訊息記錄至檔案。 本節中，您以預設模組編譯和執行 `hello_world` 應用程式。

若要編譯和執行 `hello_world` 應用程式，請在主機電腦上依照下列步驟進行︰

1. 執行下列命令以初始化組態檔：

   ```bash
   cd iot-hub-c-intel-nuc-gateway-getting-started
   cd Lesson5
   npm install
   gulp init
   ```

1. 以 Intel NUC 的 MAC 位址更新閘道器組態檔。 如果您已經完成[設定和執行 BLE 範例應用程式][config_ble]的課程，請略過此步驟。

   1. 執行下列命令以開啟閘道器組態檔：

      ```bash
      # For Windows command prompt
      code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json

      # For MacOS or Ubuntu
      code ~/.iot-hub-getting-started/config-gateway.json
      ```

   1. 當您[將 Intel NUC 設定為 IoT 閘道器][setup_nuc]時，更新閘道器的 MAC 位址，然後儲存檔案。

1. 執行下列命令以編譯範例原始程式碼：

   ```bash
   gulp compile
   ```

   此命令會將範例原始程式碼轉送至 Intel NUC，並執行`build.sh` 來編譯它。

1. 執行下列命令，在 Intel NUC 上執行 `hello_world` 應用程式︰

   ```bash
   gulp run
   ```

   此命令會執行 `config.json`中所指定的 `../Tools/run-hello-world.js`，在 Intel NUC 上啟動範例應用程式。

   ![run_sample](media/iot-hub-gateway-kit-lessons/lesson5/run_sample.png)

<a id="create-a-new-module-and-compile-it-on-intel-nuc" class="xliff"></a>

## 建立新的模組，並在 Intel NUC 上編譯

下列步驟逐步引導您建立新的模組，並在 Intel NUC 上進行編譯。 此模組收到訊息時會印出這些訊息加上時間戳記。 本節中，您將建立您的第一個自訂閘道器模組。

所有 Azure IoT Edge 模組都必須實作下列介面︰

   ```C
   pfModule_ParseConfigurationFromJson Module_ParseConfigurationFromJson
   pfModule_FreeConfiguration Module_FreeConfiguration
   pfModule_Create Module_Create
   pfModule_Destroy Module_Destroy
   pfModule_Receive Module_Receive
   ```

您可以選擇性地實作下列介面︰

   ```C
   pfModule_Start Module_Start
   ```

下圖顯示模組的重要狀態路徑。 正方形代表您實作的方法，可在模組移轉狀態時執行作業。 橢圓形是模組可能的主要狀態。

![state_path](media/iot-hub-gateway-kit-lessons/lesson5/state_path.png)

現在讓我們根據範本建立模組︰

1. 執行下列命令以開啟範本資料夾：

   ```bash
   code module/my_module
   ```

   ![code_module](media/iot-hub-gateway-kit-lessons/lesson5/code_module.png)

   - `src/my_module.c` 當作範本協助建立模組。 此範本宣告介面。 您只需要將邏輯新增至 `MyModule_Receive` 函式即可。
   - `build.sh` 是組建指令碼，用於在 Intel NUC 上編譯模組。
1. 開啟 `src/my_module.c` 檔案並加入兩個標頭檔︰

   ```C
   #include <stdio.h>
   #include "azure_c_shared_utility/xlogging.h"
   ```

1. 將下列程式碼新增至 `MyModule_Receive` 函式：

   ```C
   if (message == NULL)
   {
      LogError("invalid arg message");
   }
   else
   {
      // get the message content
      const CONSTBUFFER * content = Message_GetContent(message);
      // get the local time and format it
      time_t temp = time(NULL);
      if (temp == (time_t)-1)
      {
          LogError("time function failed");
      }
      else
      {
          struct tm* t = localtime(&temp);
          if (t == NULL)
          {
              LogError("localtime failed");
          }
          else
          {
              char timetemp[80] = { 0 };
              if (strftime(timetemp, sizeof(timetemp) / sizeof(timetemp[0]), "%c", t) == 0)
              {
                  LogError("unable to strftime");
              }
              else
              {
                  printf("[%s] %.*s\r\n", timetemp, (int)content->size, content->buffer);
              }
          }
      }
   }
   ```

1. 更新 `config.json` 檔，指定您主機電腦上的 `workspace` 資料夾和 Intel NUC 上的部署路徑。 在編譯期間，`workspace` 資料夾中的檔案會轉送至部署路徑。

   1. 執行下列命令以開啟 `config.json` 檔案：

      ```bash
      code config.json
      ```

   1. 使用下列設定更新 `config.json`：

      ```json
      "workspace": "./module/my_module",
      "deploy_path": "module/my_module"
      ```

      ![config_json](media/iot-hub-gateway-kit-lessons/lesson5/config_json.png)

1. 執行下列命令以編譯模組：

   ```bash
   gulp compile
   ```

   此命令會將原始程式碼轉送至 Intel NUC，並執行 `build.sh` 來編譯模組。

<a id="add-the-module-to-the-helloworld-sample-app-and-run-the-app-on-intel-nuc" class="xliff"></a>

## 將模組新增至 hello_world 範例應用程式，然後在 Intel NUC 上執行應用程式

若要執行這項工作，請按照下列步驟進行：

1. 執行下列命令，以列出 Intel NUC 上所有可用的模組二進位檔 (.so 檔案)︰

   ```bash
   gulp modules --list
   ```

   已編譯之 `my_module` 的二進位檔路徑應該會列出如下︰

   ```path
   /root/gateway_sample/module/my_module/build/libmy_module.so
   ```

   如果您在 `config-gateway.json` 中變更預設登入使用者名稱，則二進位檔路徑的開頭為 `home/<your username>`，而不是 `root`。

1. 將 `my_module` 新增至 `hello_world` 範例應用程式：

   1. 執行下列命令以開啟 `hello_world.json` 檔案：

      ```bash
      code sample/hello_world/src/hello_world.json
      ```

   1. 將下列程式碼新增至 `modules` 區段：

      ```json
      {
       "name": "my_module",
       "loader": {
       "name": "native",
       "entrypoint": {
       "module.path": "/root/gateway_sample/module/my_module/build/libmy_module.so"
         }
        },
       "args": null
      }
      ```

      `module.path` 的值應該是 `/root/gateway_sample/module/my_module/build/libmy_module.so`。 此程式碼宣告要與閘道器相關聯的 `my_module`，以及 `module.path` 中指定之模組二進位檔的位置。
   1. 將下列程式碼新增至 `links` 區段：

      ```json
      {
        "source": "hello_world",
        "sink": "my_module"
      }
      ```

      此程式碼指定將訊息從 `hello_world` 模組轉送至 `my_module`。

      ![hello_world_json](media/iot-hub-gateway-kit-lessons/lesson5/hello_world_json.png)

1. 執行下列命令，以執行 `hello_world` 範例應用程式：

   ```bash
   gulp run --config sample/hello_world/src/hello_world.json
   ```

   `--config` 參數強制使用 `hello_world.json` 檔案執行 `run-hello-world.js` 指令碼。

   ![hello_world_new](media/iot-hub-gateway-kit-lessons/lesson5/hello_world_new.png)

恭喜！ 您現在可以看到這個新模組的行為，只是印出 "hello world" 訊息加上時間戳記，與原始 "hello_world" 模組的結果不同。

<a id="next-steps" class="xliff"></a>

## 後續步驟

您已建立新的模組，將它新增至 hello_world 範例，並在您的閘道器以新的模組執行範例應用程式。 如果您想要深入了解 Azure IoT 閘道器模組，您可以在這裡找到更多模組範例︰[https://github.com/Azure/azure-iot-gateway-sdk/tree/master/modules](https://github.com/Azure/azure-iot-gateway-sdk/tree/master/modules)。

<!-- Images and links -->

[config_ble]: iot-hub-gateway-kit-c-lesson3-configure-ble-app.md
[setup_nuc]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md

