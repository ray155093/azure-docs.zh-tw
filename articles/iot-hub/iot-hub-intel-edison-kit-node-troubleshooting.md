---
title: "將 Intel Edison (節點) 連接到 Azure IoT - 第 4 課：疑難排解 | Microsoft Docs"
description: "Intel Edison Node.js 經驗的疑難排解頁面"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "arduino 疑難排解"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-node-get-started
ms.assetid: f11c5521-8a36-44c0-baad-f5ec26ab4618
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: d77ee0be35a3f4b38c50cf9322124e4dfbba6ddc
ms.contentlocale: zh-tw
ms.lasthandoff: 01/25/2017

---
<a id="troubleshooting" class="xliff"></a>

# 疑難排解
<a id="hardware-issues" class="xliff"></a>

## 硬體問題
如需解決 Intel Edison 上常見問題的詳細資訊，請參閱[官方疑難排解頁面](https://software.intel.com/en-us/node/637974)。

<a id="nodejs-package-issues" class="xliff"></a>

## Node.js 套件問題
<a id="no-response-during-gulp-tasks" class="xliff"></a>

### gulp 工作期間沒有回應
如果您遇到執行 gulp 工作的問題，您可以新增 `--verbose` 選項以進行偵錯。 嘗試使用 `Ctrl + C` 終止目前的 gulp 工作，然後在主控台視窗中執行下列命令查看偵錯訊息。 您可能會在主控台輸出中看到詳細的錯誤訊息。 

```bash
gulp --verbose
```

<a id="npm-issues" class="xliff"></a>

### NPM 問題
嘗試使用下列命令更新您的 NPM 套件：

```bash
npm install -g npm
```

如果問題仍然存在，在本文結尾處留下您的意見，或在我們的[範例儲存機制][sample-repository]建立 GitHub 問題。

<a id="remote-debugging" class="xliff"></a>

## 遠端偵錯

<a id="run-the-sample-application-in-debug-mode" class="xliff"></a>

### 在偵錯模式中執行範例應用程式

```bash
gulp run --debug
```

偵錯引擎一旦就緒，您應該就能從主控台輸出看到 ```Debugger listening on port 5858```。

<a id="configure-vs-code-to-connect-to-the-remote-device" class="xliff"></a>

### 設定 VS Code 以連接到遠端裝置

從左側開啟 [偵錯] 面板。

按一下綠色的 [開始偵錯] \(F5) 按鈕。 VS Code 會開啟 **launch.json** 檔案，您必須更新該檔案。

使用下列內容更新 **launch.json** 檔案，使用實際裝置的 IP 位址或主機名稱取代 `[device hostname or IP address]`。  

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Attach",
            "type": "node",
            "request": "attach",
            "port": 5858,
            "address": "[device hostname or IP address]",
            "restart": false,
            "sourceMaps": false,
            "outDir": null,
            "localRoot": "${workspaceRoot}",
            "remoteRoot": null
        }
    ]
}
```

![遠端偵錯組態](media/iot-hub-intel-edison-lessons/troubleshooting/remote_debugging_configuration.png)

<a id="attach-to-the-remote-application" class="xliff"></a>

### 附加至遠端應用程式

按一下綠色的 [開始偵錯] \(F5) 按鈕，然後享受偵錯過程。

您可以閱讀 [VS Code 中的 JavaScript](https://code.visualstudio.com/docs/languages/javascript#_debugging) 以深入了解偵錯工具。

![遠端偵錯互動](media/iot-hub-intel-edison-lessons/troubleshooting/remote_debugging_interactive.png)

<a id="azure-cli-issues" class="xliff"></a>

## Azure-CLI 問題
Azure 命令列介面 (Azure CLI) 是預覽組建。 在[預覽安裝指南](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md)中尋求解決方案。 當命令未如預期般運作時，請嘗試將 Azure-cli 升級至最新版本。

如果您遇到任何與工具有關的錯誤，請在 GitHub 儲存機制的**問題**一節中提出[問題](https://github.com/Azure/azure-cli/issues)。

如需針對常見問題進行疑難排解的說明，請參閱[讀我檔案](https://github.com/Azure/azure-cli/blob/master/README.rst)。

如果您看到「找不到符合需求的版本」，請執行下列命令將 pip 升級至最新版本。

```bash
python -m pip install --upgrade pip
```

<a id="python-installation-issues" class="xliff"></a>

## Python 安裝問題
<a id="legacy-installation-issues-macos" class="xliff"></a>

### 舊版安裝問題 (macOS)
安裝 **pip** 時，如果以 **su** 權限安裝舊版套件，則會擲回權限錯誤。 之所以發生這個情況，是因為先前使用 brew (macOS) 所安裝的 Python 未完整解除安裝。 先前安裝的某些 **pip** 套件是由根目錄建立，這會導致權限錯誤。 解決方案是移除根目錄安裝的這些套件。 使用下列步驟以完成此工作：

1. 請移至：/usr/local/lib/python2.7/site-packages
2. 列出根目錄建立的套件：`ls -l | grep root`
3. 從步驟 2 解除安裝套件：`sudo rm -rf {package name}`
4. 重新安裝 Python。

<a id="azure-iot-hub-issues" class="xliff"></a>

## Azure IoT 中樞問題
如果您已使用 `azure-cli` 成功佈建 Azure IoT 中樞，而且您需要工具來管理連接到 IoT 中樞的裝置，請嘗試下列工具：

<a id="device-explorer" class="xliff"></a>

### 裝置總管
[裝置總管](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer)會在您的 Windows 本機機器上執行，並且連接至 Azure 中的 IoT 中樞。 它會與下列 [IoT 中樞端點](iot-hub-devguide.md)通訊：

- _裝置身分識別管理_以佈建和管理已向 IoT 中樞註冊的裝置。
- _接收裝置到雲端_以便可以監視從裝置傳送到 IoT 中樞的訊息。
- _傳送裝置到雲端_以便可以從 IoT 中樞將訊息傳送至裝置。

在此工具內設定您的 `IoT hub connection string`以使用它的所有功能。

<a id="iot-hub-explorer" class="xliff"></a>

### IoT 中樞總管
[IoT 中樞總管](https://github.com/Azure/iothub-explorer)是範例多平台 CLI 工具，可以管理裝置用戶端。 您可以使用此工具管理身分識別登錄中的裝置、監視裝置到雲端訊息，以及傳送雲端到裝置命令。

若要安裝最新 (發行前版本) 版本 iothub-explorer 工具，請在命令列環境中執行下列命令︰

```bash
npm install -g iothub-explorer@latest
```

您可以使用下列命令取得所有 iothub-explorer 命令及其參數的額外說明：

```bash
iothub-explorer help
```

<a id="azure-portal" class="xliff"></a>

### Azure 入口網站
完整的 CLI 體驗可協助您建立及管理您所有的 Azure 資源。 您可能也會想要使用 [Azure 入口網站](../azure-portal-overview.md)以協助佈建、管理和偵錯您的 Azure 資源。

<a id="azure-storage-issues" class="xliff"></a>

## Azure 儲存體問題
[Microsoft Azure 儲存體總管 (預覽)](http://storageexplorer.com) 是 Microsoft 所提供的獨立應用程式，可供您在 Windows、macOS 和 Linux 上處理 [Azure 儲存體](https://azure.microsoft.com/en-us/services/storage/)資料。 透過使用此工具，您可以連接到資料表，並查看其中的資料。 您可以使用這項工具，對 Azure 儲存體問題進行疑難排解。

<a id="next-steps" class="xliff"></a>

## 後續步驟
此頁面只包括 Intel Edison 套件最常見的問題。 您也可以在底部留下註解來報告問題，以便進行進一步的疑難排解。

返回[開始使用 Intel Edison (Node.js)](iot-hub-intel-edison-kit-node-get-started.md)

<!-- Images and links -->

[sample-repository]: https://github.com/Azure-Samples/iot-hub-node-edison-getting-started
