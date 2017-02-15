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
ms.date: 09/21/2016
ms.author: boltean
translationtype: Human Translation
ms.sourcegitcommit: 39c8c4944ef19379dc04e04a717ab60d305593c4
ms.openlocfilehash: 08fe56605555342ec14e1628f77d905783fda057


---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>使用 Azure CLI 建立 IoT 中樞
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>簡介
您可以使用 Azure CLI (azure.js)，以程式設計方式建立和管理 Azure IoT 中樞。 本文將說明如何使用 Azure CLI (azure.js) 建立 IoT 中樞。

您可以使用下列其中一個 CLI 版本來完成工作︰

* Azure CLI (azure.js) – 適用於傳統和資源管理部署模型的 CLI (如本文中所述)。
* [Azure CLI 2.0 (預覽) (az.py)](iot-hub-create-using-cli.md) - 適用於資源管理部署模型的新一代 CLI。

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。
* [Azure CLI 0.10.4][lnk-CLI-install] 或更新版本。 如果您已經有 Azure CLI，您可以在命令提示字元中使用下列命令驗證目前的版本︰
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
1. 在命令提示字元登入中輸入下列命令
   
   ```
    azure login
   ```
   使用建議的網頁瀏覽器和程式碼來驗證。
2. 如果您擁有多個 Azure 訂用帳戶，連接到 Azure 將會針對與您的認證相關聯的所有 Azure 訂用帳戶授予存取權限。 您可以使用命令來檢視 Azure 訂用帳戶及預設的訂用帳戶
   
   ```
    azure account list 
   ```

   若要設定您要在其下執行剩餘命令的訂用帳戶內容，請使用

   ```
    azure account set <subscription name>
   ```

3. 如果您沒有資源群組，您可以建立一個名為 **exampleResourceGroup** 的資源群組 
   ```
    azure group create -n exampleResourceGroup -l westus
   ```

> [!TIP]
> [使用 Azure CLI 管理 Azure 資源和資源群組][lnk-CLI-arm]一文提供關於如何使用 Azure CLI 管理 Azure 資源的詳細資訊。 
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
若要查看可用來建立的所有參數，您可以在命令提示字元中使用 help 命令

```
    azure iothub create -h 
```
快速範例︰

 若要在資源群組 **exampleResourceGroup** 中建立名為 **exampleIoTHubName** 的IoT 中樞，只要執行下列命令即可

```
    azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [!NOTE]
> 此 Azure CLI 命令會新增您付費的「S1 標準 IoT 中樞」。 您可以使用下列命令刪除 IoT 中樞 **exampleIoTHubName** 
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
[lnk-CLI-install]: ../xplat-cli-install.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-CLI-arm]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 



<!--HONumber=Dec16_HO2-->


