---
title: "將 Arduino 連線至 Azure IoT - 第 2 課：註冊裝置 | Microsoft Docs"
description: "使用 Azure CLI 建立資源群組、建立 Azure IoT 中樞，並在 Azure IoT 中樞登錄 Adafruit Feather M0 WiFi。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "將 arduino 連線至雲端, azure iot 中樞, 物聯網雲端, azure iot 中樞建立裝置, arduino 雲端"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started
ms.assetid: 5edc690b-7a1d-4ebc-b011-ff27bfffe6e8
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: ac9421be9211d53f8b5239c356201ee3efd27999
ms.contentlocale: zh-tw
ms.lasthandoff: 03/10/2017

---
<a id="create-your-iot-hub-and-register-your-adafruit-feather-m0-wifi-arduino-board" class="xliff"></a>

# 建立 IoT 中樞並登錄 Adafruit Feather M0 WiFi Arduino 面板

<a id="what-you-will-do" class="xliff"></a>

## 將執行的作業
* 建立資源群組。
* 在資源群組中建立 Azure IoT 中樞。
* 使用 Azure 命令列介面 (Azure CLI)，將 Arduino 面板新增至 Azure IoT 中樞。

當您使用 Azure CLI 將 Arduino 面板新增至 IoT 中樞時，服務將會為您的 Arduino 面板產生一組金鑰，以向服務進行驗證。 如果您有任何問題，請在[疑難排解頁面][troubleshoot]尋求解決方案。

<a id="what-you-will-learn" class="xliff"></a>

## 學習目標
在本文中，您將了解：
* 如何使用 Azure CLI 建立 IoT 中樞。
* 如何在 IoT 中樞中為您的 Arduino 面板建立裝置識別。

<a id="what-you-need" class="xliff"></a>

## 您需要什麼
* 一個 Azure 帳戶
* 一部已安裝 Azure CLI 的電腦

<a id="create-your-iot-hub" class="xliff"></a>

## 建立 IoT 中樞
Azure IoT 中樞可以協助您連接、監視並管理數以百萬計的 IoT 資產。 若要建立 IoT 中樞，請遵循下列步驟：

1. 執行下列命令來登入您的 Azure 帳戶：

   ```bash
   az login
   ```

   成功登入之後，系統將會列出所有可用的訂用帳戶。

2. 執行下列命令來設定您想要使用的預設訂用帳戶：

   ```bash
   az account set --subscription {subscription id or name}
   ```

   `subscription ID or name` 可在 `az login` 或 `az account list` 命令的輸出中找到。

3. 執行下列命令來登錄提供者。 資源提供者是為應用程式提供資源的服務。 您必須先登錄提供者，才能部署該提供者所提供的 Azure 資源。

   ```bash
   az provider register -n "Microsoft.Devices"
   ```
4. 執行下列命令來在美國西部區域建立名為 iot-sample 的資源群組：

   ```bash
   az group create --name iot-sample --location westus
   ```

   `westus` 是資源群組建立所在的位置。 如果您想要使用另一個位置，您可以執行 `az account list-locations -o table` 來查看 Azure 支援的所有位置。

5. 執行下列命令來在 iot-sample 資源群組中建立 IoT 中樞：

   ```bash
   az iot hub create --name {my hub name} --resource-group iot-sample
   ```

根據預設，此工具會在免費定價層建立 IoT 中樞。 如需詳細資訊，請參閱 [Azure IoT 中樞價格](https://azure.microsoft.com/pricing/details/iot-hub/)。

> [!NOTE]
> 您 IoT 中樞的名稱必須是全域唯一的。
> 您的 Azure 訂用帳戶只能建立一個 F1 版本的 Azure IoT 中樞。

<a id="register-your-arduino-board-in-your-iot-hub" class="xliff"></a>

## 在 IoT 中樞登錄您的 Arduino 面板
每一個向/從 IoT 中樞傳送/接收訊息的裝置，都必須以唯一識別碼登錄。

執行下列命令在 Azure IoT 中樞中登錄您的 Arduino 面板：

```bash
az iot device create --device-id mym0wifi --hub-name {my hub name}
```

<a id="summary" class="xliff"></a>

## 摘要
您已建立 IoT 中樞，並在 IoT 中樞中登錄您的 Arduino 面板及裝置身分識別。 您已準備好了解如何從 Arduino 面板傳送訊息至 IoT 中樞。

<a id="next-steps" class="xliff"></a>

## 後續步驟
[建立 Azure 函式應用程式和 Azure 儲存體帳戶以處理並儲存 IoT 中樞訊息][process-and-store-iot-hub-messages]。


<!-- Images and links -->

[troubleshoot]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
