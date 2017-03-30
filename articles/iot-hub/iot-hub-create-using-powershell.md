---
title: "使用 PowerShell Cmdlet 建立 Azure IoT 中樞 | Microsoft Docs"
description: "如何使用 PowerShell Cmdlet 建立 IoT 中樞。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 5c6ab3a8fdacb43546801518b76a15314d0b4b0f
ms.lasthandoff: 03/21/2017


---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>使用 New-AzureRmIotHub Cmdlet 建立 IoT 中樞

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>簡介

您可以使用 Azure PowerShell Cmdlet 建立及管理 Azure IoT 中樞。 此教學課程會示範如何使用 PowerShell 建立 IoT 中樞。

> [!NOTE]
> Azure 有兩種不同的部署模型可建立和處理資源：[Azure Resource Manager 和傳統](../azure-resource-manager/resource-manager-deployment-model.md)。  本文涵蓋使用 Azure Resource Manager 部署模型的部分。

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。 <br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。
* [Azure PowerShell 1.0][lnk-powershell-install] 或更新版本。
* [Azure Resource Manager Cmdlet][lnk-rm-install]。

## <a name="connect-to-your-azure-subscription"></a>連接到 Azure 訂用帳戶
在 PowerShell 命令提示字元中，輸入下列命令來登入您的 Azure 訂用帳戶：

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>建立資源群組

您需要一個資源群組來部署 IoT 中樞。 您可以使用現有的資源群組，或建立一個新的群組。

您可以使用下列命令，探索您可以在其中部署 IoT 中樞的位置：

```powershell
((Get-AzureRmResourceProvider `
  -ProviderNamespace Microsoft.Devices).ResourceTypes `
  | Where-Object ResourceTypeName -eq IoTHubs).Locations
```

若要在下列其中一個支援的 IoT 中樞位置中建立 IoT 中樞的資源群組，請使用下列命令。 此範例會在**美國東部**區域中建立一個稱為 **MyIoTRG1** 的資源群組：

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

若要在您於上一個步驟中建立的資源群組中建立 IoT 中樞，請使用下列命令。 此範例會在**美國東部**區域中建立一個稱為 **MyTestIoTHub** 的 **S1** 中樞：

```powershell
New-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

> [!NOTE]
> IoT 中樞名稱必須是唯一的。

您可以使用下列命令來列出您訂用帳戶中的所有 IoT 中樞︰

```powershell
Get-AzureRmIotHub
```

前一個範例會新增一個向您收費的 S1 標準 IoT 中樞。 您可以使用下列命令刪除該 IoT 中樞：

```powershell
Remove-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

或者，您可以使用下列命令移除資源群組，及其包含的所有資源：

```powershell
Remove-AzureRmResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>後續步驟

現在您已經使用 PowerShell Cmdlet 部署 IoT 中樞，您可能想要進一步探索︰

* 探索其他[搭配您的 IoT 中樞使用的 PowerShell Cmdlet][lnk-iothub-cmdlets]。
* 閱讀 [IoT 中樞資源提供者 REST API][lnk-rest-api] 功能的相關資訊。

若要深入了解如何開發 IoT 中樞，請參閱以下文章︰

* [C SDK 簡介][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

若要進一步探索 IoT 中樞的功能，請參閱︰

* [使用 IoT 閘道 SDK 來模擬裝置][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-powershell-install]: /powershell/azureps-cmdlets-docs
[lnk-iothub-cmdlets]: /powershell/resourcemanager/azurerm.iothub/v1.3.0/azurerm.iothub
[lnk-rm-install]: /powershell/resourcemanager/
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

