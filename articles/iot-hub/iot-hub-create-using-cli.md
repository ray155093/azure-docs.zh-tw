<properties
    pageTitle="使用 CLI 建立 IoT 中樞 | Microsoft Azure"
    description="請依照這篇文章，使用 Azure 命令列介面建立 IoT 中樞。"
    services="iot-hub"
    documentationCenter=".net"
    authors="BeatriceOltean"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/21/2016"
     ms.author="boltean"/>


# <a name="create-an-iot-hub-using-cli"></a>使用 CLI 建立 IoT 中樞

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>簡介

您可以使用 Azure 命令列介面，以程式設計方式建立和管理 Azure IoT 中樞。 本文將說明如何使用 Azure CLI 建立 IoT 中樞。

若要完成此教學課程，您需要下列項目：

- 使用中的 Azure 帳戶。 只需要幾分鐘的時間，您就可以建立 [Azure 免費試用][lnk-free-trial] 帳戶。
- [Azure CLI 0.10.4][lnk-CLI-install] 或更新版本。 如果您已經有 Azure CLI，您可以在命令提示字元中使用下列命令驗證目前的版本︰
```
    azure --version
```

> [AZURE.NOTE] Azure 建立和處理資源的部署模型有二種：[Resource Manager 和傳統](../resource-manager-deployment-model.md)。 Azure CLI 必須處於 Azure Resource Manager 模式：
```
    azure config mode arm
```

## <a name="set-your-azure-account-and-subscription"></a>設定 Azure 帳戶和訂用帳戶 

1. 在命令提示字元登入中輸入下列命令
```
    azure login
```
使用建議的網頁瀏覽器和程式碼來驗證。

2. 如果您擁有多個 Azure 訂用帳戶，連接到 Azure 將會針對與您的認證相關聯的所有訂用帳戶授予存取權限。 您可以使用命令來檢視訂用帳戶及預設的訂用帳戶
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

> [AZURE.TIP] 文章[使用 Azure CLI 管理 Azure 資源和資源群組][lnk-CLI-arm] 提供關於如何使用 Azure CLI 管理 Azure 資源的詳細資訊。 


## <a name="create-and-iot-hub"></a>建立 IoT 中樞

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

> [AZURE.NOTE] 此 CLI 命令會新增您付費的「S1 標準 IoT 中樞」。 您可以使用下列命令刪除 IoT 中樞 **exampleIoTHubName** 
```
    azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
```


## <a name="next-steps"></a>後續步驟
若要深入了解如何開發 IoT 中樞，請參閱以下內容︰
- [IoT 中樞 SDK][lnk-sdks]

若要進一步探索 IoT 中樞的功能，請參閱︰

- [使用 Azure 入口網站管理 IoT 中樞][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]: ../xplat-cli-install.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-CLI-arm]: ../xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 



<!--HONumber=Oct16_HO2-->


