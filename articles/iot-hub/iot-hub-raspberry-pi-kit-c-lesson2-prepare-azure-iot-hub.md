---
title: "建立 IoT 中樞並登錄 Raspberry Pi 3 | Microsoft Docs"
description: "使用 Azure CLI 建立資源群組、建立 Azure IoT 中樞，並在 Azure IoT 中樞登錄 Pi。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "raspberry pi 雲端, pi 雲端連線"
ms.assetid: 4bcfb071-b3ae-48cc-8ea5-7e7434732287
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 155e5d6280d86b06b1718fc3032c2c224539183d
ms.openlocfilehash: 4fb7280cbf9221d1fe8b9fae24356e367969d32a


---
# <a name="create-your-iot-hub-and-register-raspberry-pi-3"></a>建立 IoT 中樞並登錄 Raspberry Pi 3
## <a name="what-you-will-do"></a>將執行的作業
* 建立資源群組。
* 在資源群組中建立 Azure IoT 中樞。
* 使用 Azure 命令列介面 (Azure CLI)，將 Raspberry Pi 3 新增至 Azure IoT 中樞。

當您使用 Azure CLI 將 Pi 新增至 IoT 中樞時，服務將會為 Pi 產生金鑰，以向服務進行驗證。 如果您有任何問題，請在[疑難排解頁面](iot-hub-raspberry-pi-kit-c-troubleshooting.md)尋求解決方案。

## <a name="what-you-will-learn"></a>學習目標
在本文中，您將了解：
* 如何使用 Azure CLI 建立 IoT 中樞。
* 如何在 IoT 中樞為 Pi 建立裝置身分識別。

## <a name="what-you-need"></a>您需要什麼
* 一個 Azure 帳戶
* 一部已安裝 Azure CLI 的 Mac 或 Windows 電腦

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
> 您 IoT 中樞的名稱必須是全域唯一的。 您的 Azure 訂用帳戶只能建立一個 F1 版本的 Azure IoT 中樞。

## <a name="register-pi-in-your-iot-hub"></a>在 IoT 中樞中登錄 Pi
每一個向/從 IoT 中樞傳送/接收訊息的裝置，都必須以唯一識別碼登錄。

執行下列命令在中樞登錄 Pi：

```bash
az iot device create --device-id myraspberrypi --hub {my hub name} --resource-group iot-sample
```

## <a name="summary"></a>摘要
您已建立 IoT 中樞，並在 IoT 中樞中搭配裝置識別登錄 Pi。 您已準備好了解如何從 Pi 傳送訊息至 IoT 中樞。

## <a name="next-steps"></a>後續步驟
[建立 Azure 函式應用程式和 Azure 儲存體帳戶以處理並儲存 IoT 中樞訊息](iot-hub-raspberry-pi-kit-c-lesson3-deploy-resource-manager-template.md)




<!--HONumber=Dec16_HO1-->


