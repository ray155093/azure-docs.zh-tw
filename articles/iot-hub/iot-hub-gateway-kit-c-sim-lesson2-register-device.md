---
title: "模擬裝置與 Azure IoT 閘道 - 第 2 課：登錄裝置 | Microsoft Docs"
description: 
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure iot 中樞, 物聯網雲端, azure iot 中樞建立裝置, ti sensortag, ti ble"
ms.assetid: 23cfbe21-22c6-4fe1-ae41-63714a897f12
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 759085dcb4993d38f79cb19efe58e1a968ddb682


---

# <a name="create-your-azure-iot-hub-and-register-your-device"></a>建立 IoT 中樞並登錄您的裝置

## <a name="what-you-will-do"></a>將執行的作業

- 建立資源群組
- 建立您的第一個 IoT 中樞
- 使用 Azure CLI 在 IoT 中樞登錄您的裝置。 

當您在 IoT 中樞登錄您的裝置時，Azure IoT 中樞服務將會為您的裝置產生一個金鑰，用以向服務進行驗證。 

如果您有任何問題，請在[疑難排解頁面](iot-hub-gateway-kit-c-sim-troubleshooting.md)尋求解決方案。

## <a name="what-you-will-learn"></a>學習目標

在這一課，您將了解：

- 如何使用 Azure CLI 建立 IoT 中樞。
- 如何在 IoT 中樞登錄裝置。

## <a name="what-you-need"></a>您需要什麼

- 有效的 Azure 訂用帳戶。 如果您沒有 Azure 帳戶，只需要幾分鐘的時間就可以建立[免費的 Azure 試用帳戶](http://azure.microsoft.com/pricing/free-trial/)。
- 您應該已經安裝 Azure CLI。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

若要建立 IoT 中樞，遵循下列步驟：

1. 執行下列命令來登入您的 Azure 帳戶：

   ```bash
   az login
   ```

   成功登入之後，系統會列出所有可用的訂用帳戶。

2. 執行下列命令來設定您想要使用的預設 Azure 訂用帳戶：

   ```bash
   az account set --subscription {subscription id or name}
   ```

   `subscription ID or name` 可在 `az login` 或 `az account list` 命令的輸出中找到。

3. 執行下列命令來登錄提供者。 資源提供者是為應用程式提供資源的服務。 您必須先登錄提供者，才能部署該提供者所提供的 Azure 資源。

   ```bash
   az provider register -n "Microsoft.Devices"
   ```

4. 執行下列命令在美國西部區域建立名為 `iot-gateway` 的資源群組：

   ```bash
   az group create --name iot-gateway --location westus
   ```
   
   `westus` 是資源群組建立所在的位置。 如果您想要使用另一個位置，您可以執行 `az account list-locations -o table` 來查看 Azure 支援的所有位置。

5. 執行下列命令在 `iot-gateway` 資源群組中建立 IoT 中樞：

   ```bash
   az iot hub create --name {my hub name} --resource-group iot-gateway
   ```

根據預設，此工具會在免費定價層建立 IoT 中樞。 如需詳細資訊，請參閱 [Azure IoT 中樞價格](https://azure.microsoft.com/pricing/details/iot-hub/)。

> [!NOTE]
> 您 IoT 中樞的名稱必須是全域唯一的。 您的 Azure 訂用帳戶只能建立一個 F1 版本的 Azure IoT 中樞。

## <a name="register-your-device-in-your-iot-hub"></a>在 IoT 中樞登錄您的裝置

每一個向/從 IoT 中樞傳送/接收訊息的裝置，都必須以唯一識別碼登錄。
執行下列命令在 Azure IoT 中樞中登錄您的裝置：

```bash
az iot device create --device-id mydevice --hub-name {my hub name} --resource-group iot-gateway
```

## <a name="summary"></a>摘要

您已建立 IoT 中樞，並在 IoT 中樞中登錄您的邏輯裝置及裝置身分識別。 您可以開始了解如何設定及執行閘道器範例應用程式，將資料從您的實體裝置傳送至您位於雲端的 IoT 中樞。

## <a name="next-steps"></a>後續步驟
[設定及執行模擬裝置雲端上傳範例應用程式](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md)


<!--HONumber=Jan17_HO4-->


