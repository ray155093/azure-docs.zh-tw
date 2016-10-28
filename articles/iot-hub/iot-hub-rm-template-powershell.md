<properties
	pageTitle="使用 Resource Manager 範本與 PowerShell 建立 IoT 中樞 |Microsoft Azure"
	description="請依照此教學課程的說明，開始使用資源管理員範本搭配 PowerShell 來建立 IoT 中樞。"
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/07/2016"
     ms.author="dobett"/>

# 使用 PowerShell 建立 IoT 中樞

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## 簡介

您可以使用 Azure 資源管理員，以程式設計方式建立和管理 Azure IoT 中樞。本教學課程示範如何使用 Resource Manager 範本搭配 PowerShell 來建立 IoT 中樞。

> [AZURE.NOTE] Azure 建立和處理資源的部署模型有二種：[資源管理員和傳統](../resource-manager-deployment-model.md)。本文涵蓋內容包括如何使用 Resource Manager 部署模型。

若要完成此教學課程，您需要下列項目：

- 使用中的 Azure 帳戶。<br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][lnk-free-trial]。
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] 或更新版本。

> [AZURE.TIP] [搭配使用 Azure PowerShell 與 Azure Resource Manager][lnk-powershell-arm] 一文提供有關如何使用 PowerShell 指令碼和 Resource Manager 範本來建立 Azure 資源的詳細資訊。

## 連線到 Azure 訂閱

在 PowerShell 命令提示字元中，輸入下列命令來登入您的 Azure 訂用帳戶：

```
Login-AzureRmAccount
```

您可以使用下列命令來探索可部署 IoT 中樞的位置和目前支援的 API 版本：

```
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

在 IoT 中樞支援的其中一個位置，使用下列命令建立資源群組來包含您的 IoT 中樞。這個範例會建立一個稱為 **MyIoTRG1** 的資源群組：

```
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## 提交範本，以建立 IoT 中樞

使用 JSON 範本在資源群組中建立 IoT 中樞。您也可以使用範本變更現有的 IoT 中樞。

1. 使用文字編輯器來建立名為 **template.json** 的 Resource Manager 範本，其中包含下列資源定義來建立新的標準 IoT 中樞。這個範例會在**美國東部**區域加入 IoT 中樞、在事件中樞相容端點上建立兩個取用者群組 (**cg1** 和 **cg2**)，並使用 **2016-02-03** API 版本。這個範本也要求您在一個名為 **hubName** 的參數中傳入 IoT 中樞名稱。如需目前支援「IoT 中樞」的位置清單，請參閱 [Azure 狀態][lnk-status]。

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

2. 將範本檔案儲存在本機電腦上。這個範例假設您將它儲存在名為 **c:\\templates** 的資料夾中。

3. 執行下列命令來部署新的 IoT 中樞，並傳遞 IoT 中樞的名稱做為參數。在此範例中，IoT 中樞名稱是 **abcmyiothub** (請注意，此名稱必須是全域唯一的，因此應該包含您的名稱或縮寫)：

    ```
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```

4. 輸出會顯示您建立的 IoT 中樞的金鑰。

5. 若要確認應用程式是否新增了 IoT 中樞，可前往[入口網站][lnk-azure-portal]檢視您的資源清單，或是使用 **Get-AzureRmResource** PowerShell Cmdlet。

> [AZURE.NOTE] 此範例應用程式會加入您付費的「S1 標準 IoT 中樞」。您可透過[入口網站][lnk-azure-portal]刪除此 IoT 中樞，或在完成時，使用 **Remove-AzureRmResource** PowerShell Cmdlet。

## 後續步驟

現在您已使用 Resource Manager 範本搭配 PowerShell 來部署 IoT 中樞，您可以再進一步探索：

- 閱讀 [IoT 中樞資源提供者 REST API][lnk-rest-api] 功能的相關資訊。
- 如需 Azure 資源管理員功能的詳細資訊，請參閱 [Azure 資源管理員概觀][lnk-azure-rm-overview]。

若要深入了解如何開發 IoT 中樞，請參閱以下內容︰

- [C SDK 簡介][lnk-c-sdk]
- [IoT 中心 SDK][lnk-sdks]

若要進一步探索 IoT 中樞的功能，請參閱︰

- [設計您的解決方案][lnk-design]
- [使用範例 UI 探索裝置管理][lnk-dmui]
- [使用閘道 SDK 模擬裝置][lnk-gateway]
- [使用 Azure 入口網站管理 IoT 中樞][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-powershell-arm]: ../powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-sdks-summary.md

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!----HONumber=AcomDC_0907_2016-->