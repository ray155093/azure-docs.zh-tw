---
title: "將 Intel Edison (節點) 連接到 Azure IoT - 第 2 課：登錄裝置 | Microsoft Docs"
description: "使用 Azure CLI 建立資源群組、建立 Azure IoT 中樞，並在 Azure IoT 中樞中登錄 Edison。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: c1465cc2-d0d9-4326-967c-64d95b61dd54
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 384724ccf78dc08b215ef602ff4366dd54801ab5
ms.lasthandoff: 01/25/2017


---
# <a name="create-your-iot-hub-and-register-intel-edison"></a>建立 IoT 中樞並登錄 Intel Edison
## <a name="what-you-will-do"></a>將執行的作業
* 建立資源群組。
* 在資源群組中建立 Azure IoT 中樞。
* 使用 Azure 命令列介面 (Azure CLI)，將 Intel Edison 新增至 Azure IoT 中樞。

當您使用 Azure CLI 將 Edison 加入 IoT 中樞時，服務將會為 Edison 產生一組金鑰，以向服務進行驗證。 如果您有任何問題，請在[疑難排解頁面][troubleshooting]尋求解決方案。

## <a name="what-you-will-learn"></a>學習目標
在本文中，您將了解：
* 如何使用 Azure CLI 建立 IoT 中樞。
* 如何在 IoT 中樞為 Edison 建立裝置身分識別。

## <a name="what-you-need"></a>您需要什麼
* 一個 Azure 帳戶。 如果您沒有 Azure 帳戶，請花幾分鐘的時間建立[免費的 Azure 試用帳戶](http://azure.microsoft.com/pricing/free-trial/)。
* 您應該已經安裝 Azure CLI。

## <a name="create-your-iot-hub"></a>建立 IoT 中樞
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


## <a name="register-edison-in-your-iot-hub"></a>在 IoT 中樞中登錄 Edison
每一個向/從 IoT 中樞傳送/接收訊息的裝置，都必須以唯一識別碼登錄。

執行下列命令在 Azure IoT 中樞中登錄 Edison：

```bash
az iot device create --device-id myinteledison --hub-name {my hub name}
```

## <a name="summary"></a>摘要
您已建立 IoT 中樞，並在 IoT 中樞中搭配裝置識別登錄 Edison。 您已準備好了解如何從 Edison 傳送訊息至 IoT 中樞。

## <a name="next-steps"></a>後續步驟
[建立 Azure 函式應用程式和 Azure 儲存體帳戶以處理並儲存 IoT 中樞訊息][process-and-store-iot-hub-messages]。


<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-intel-edison-kit-node-lesson3-deploy-resource-manager-template.md
