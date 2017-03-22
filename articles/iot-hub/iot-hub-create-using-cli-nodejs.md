---
title: "使用 Azure CLI (azure.js) 建立 IoT 中樞 | Microsoft Docs"
description: "如何使用跨平台 Azure CLI (azure.js) 建立 Azure IoT 中樞。"
services: iot-hub
documentationcenter: .net
author: BeatriceOltean
manager: timlt
editor: 
ms.assetid: 46a17831-650c-41d9-b228-445c5bb423d3
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: boltean
translationtype: Human Translation
ms.sourcegitcommit: 5ea7095e12b6194556d3cd0baa43ccfed1e087ee
ms.openlocfilehash: 1be7dc038f1bc864e7c6461b616449d75ecda439
ms.lasthandoff: 02/27/2017


---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>使用 Azure CLI 建立 IoT 中樞
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>簡介
您可以使用 Azure CLI (azure.js)，以程式設計方式建立和管理 Azure IoT 中樞。 本文將說明如何使用 Azure CLI (azure.js) 建立 IoT 中樞。

您可以使用下列其中一個 CLI 版本來完成工作︰

* Azure CLI (azure.js) – 適用於傳統和資源管理部署模型的 CLI (如本文中所述)。
* [Azure CLI 2.0 (az.py)](iot-hub-create-using-cli.md) - 適用於資源管理部署模型的新一代 CLI。

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。
* [Azure CLI 0.10.4][lnk-CLI-install] 或更新版本。 如果您已經安裝 Azure CLI，您可以在命令提示字元中使用下列命令來驗證目前的版本︰
  ```
    azure --version
  ```

> [!NOTE]
> Azure 有兩種不同的部署模型可建立和處理資源：[Azure Resource Manager 和傳統](../azure-resource-manager/resource-manager-deployment-model.md)。 Azure CLI 必須處於 Azure Resource Manager 模式：
> 
> ```
> azure config mode arm
> ```
> 
> 

## <a name="set-your-azure-account-and-subscription"></a>設定 Azure 帳戶和訂用帳戶
1. 在命令提示字元中，輸入下列命令來進行登入：
   
   ```
    azure login
   ```
   使用建議的網頁瀏覽器和程式碼來驗證。
2. 如果您有多個 Azure 訂用帳戶，則連接到 Azure 會將與您認證關聯的所有 Azure 訂用帳戶的存取權都授與您。 您可以使用下列命令來檢視 Azure 訂用帳戶，以及識別哪個帳戶是預設訂用帳戶：
   
   ```
    azure account list 
   ```

   若要設定您要在其下執行剩餘命令的訂用帳戶內容，請使用：

   ```
    azure account set <subscription name>
   ```

3. 如果您沒有資源群組，您可以建立一個名為 **exampleResourceGroup** 的資源群組：
   ```
    azure group create -n exampleResourceGroup -l westus
   ```

> [!TIP]
> [使用 Azure CLI 管理 Azure 資源和資源群組 (英文)][lnk-CLI-arm] 一文提供關於如何使用 Azure CLI 來管理 Azure 資源的詳細資訊。 
> 
> 

## <a name="create-an-iot-hub"></a>建立 IoT 中樞
必要參數：

```
 azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>  
    - <resourceGroup> The resource group name (case insensitive alphanumeric, underscore and hyphen, 1-64 length)
    - <name> (The name of the IoT hub to be created. The format is case insensitive alphanumeric, underscore and hyphen, 3-50 length )
    - <location> (The location (azure region/datacenter) where the IoT hub will be provisioned.
    - <sku-name> (The name of the sku, one of: [F1, S1, S2, S3] etc. For the latest full list refer to the pricing page for IoT Hub.
    - <units> (The number of provisioned units. Range : F1 [1-1] : S1, S2 [1-200] : S3 [1-10]. IoT Hub units are based on your total message count and the number of devices you want to connect.)
```
若要查看可用來建立的所有參數，您可以在命令提示字元中使用 help 命令：

```
    azure iothub create -h 
```
快速範例︰

 若要在資源群組 **exampleResourceGroup** 中建立名為 **exampleIoTHubName** 的「IoT 中樞」，請執行下列命令：

```
    azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [!NOTE]
> 此 Azure CLI 命令會新增您付費的「S1 標準 IoT 中樞」。 您可以使用下列命令來刪除 IoT 中樞 **exampleIoTHubName**：
> 
> ```
> azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
> ```
> 
> 
> 

## <a name="next-steps"></a>後續步驟
若要深入了解如何開發 IoT 中樞，請參閱以下內容︰

* [IoT SDK][lnk-sdks]

若要進一步探索 IoT 中樞的功能，請參閱︰

* [使用 Azure 入口網站管理 IoT 中樞][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]:../cli-install-nodejs.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-CLI-arm]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 

