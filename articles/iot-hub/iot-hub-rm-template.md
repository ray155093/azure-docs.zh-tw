---
title: "使用 ARM 範本和 C# 建立 IoT 中樞 | Microsoft Docs"
description: "請依照此教學課程的說明，開始使用 Azure Resource Manager 範本搭配 C# 程式來建立「IoT 中樞」。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a447b40c-c728-487e-875d-db554db5adc3
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: 118ab90d7886ad48e7476abd732b16b69375d214


---
# <a name="create-an-iot-hub-using-a-c-program-with-an-azure-resource-manager-template"></a>使用 C# 程式搭配 Azure Resource Manager 範本來建立 IoT 中樞
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>簡介
您可以使用 Azure 資源管理員，以程式設計方式建立和管理 Azure IoT 中樞。 本教學課程示範如何使用 Azure Resource Manager 範本從 C# 程式建立 IoT 中樞。

> [!NOTE]
> Azure 有兩種不同的部署模型可建立和處理資源：[Azure Resource Manager 和傳統](../azure-resource-manager/resource-manager-deployment-model.md)。  本文涵蓋使用 Azure Resource Manager 部署模型的部分。
> 
> 

若要完成此教學課程，您需要下列項目：

* Microsoft Visual Studio 2015。
* 使用中的 Azure 帳戶。 <br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。
* 可供您儲存 Azure Resource Manager 範本檔案的 [Azure 儲存體帳戶][lnk-storage-account]。
* [Microsoft Azure PowerShell 1.0][lnk-powershell-install] 或更新版本。

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>準備 Visual Studio 專案
1. 在 Visual Studio 中，使用 [主控台應用程式] 專案範本來建立 Visual C# Windows 專案。 將專案命名為 **CreateIoTHub**。
2. 在方案總管中，於專案上按一下滑鼠右鍵，然後按一下 [管理 NuGet 封裝] 。
3. 在「NuGet 套件管理員」中，勾選 [包含發行前版本]，然後搜尋 **Microsoft.Azure.Management.ResourceManager**。 按一下 [安裝]，在 [檢閱變更] 中按一下 [確定]，然後按一下 [我接受] 來接受授權。
4. 在 NuGet 套件管理員中，搜尋 **Microsoft.IdentityModel.Clients.ActiveDirectory**。  按一下 [安裝]，在 [檢閱變更] 中按一下 [確定]，然後按一下 [我接受] 來接受授權。
5. 在 Program.cs 中，以下列項目取代現有的 **using** 陳述式：
   
    ```
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
6. 在 Program.cs 中，以下列靜態變數取代預留位置值。 您先前已在本教學課程中記下 **ApplicationId**、**SubscriptionId**、**TenantId** 及 **Password**。 **儲存體帳戶名稱**是您儲存 Azure Resource Manager 範本檔案之「Azure 儲存體」帳戶的名稱。 **資源群組名稱**是您建立「IoT 中樞」時所使用之資源群組的名稱，此群組可以是既存的資源群組，也可以是新的資源群組。 **部署名稱**是部署的名稱，例如 **Deployment_01**。
   
    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-an-azure-resource-manager-template-to-create-an-iot-hub"></a>提交 Azure Resource Manager 範本以建立 IoT 中樞
使用 JSON 範本和參數檔案，在資源群組中建立 IoT 中樞。 您也可以使用 Azure Resource Manager 範本來對現有的 IoT 中樞進行變更。

1. 在 [方案總管] 中，於專案上按一下滑鼠右鍵，按一下 [加入]，然後按一下 [新增項目]。 將名為 **template.json** 的 JSON 檔案新增到專案中。
2. 以下列資源定義取代 **template.json** 的內容，為**美國東部**區域加入新的標準 IoT 中樞。 如需目前支援「IoT 中樞」的區域清單，請參閱 [Azure 狀態][lnk-status]：
   
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
3. 在 [方案總管] 中，於專案上按一下滑鼠右鍵，按一下 [加入]，然後按一下 [新增項目]。 將名為 **parameters.json** 的 JSON 檔案新增到專案中。
4. 使用下列參數資訊來取代 **parameters.json** 的內容，此參數資訊會設定新 IoT 中樞的名稱，例如 **{your initials}mynewiothub**。 IoT 中樞名稱必須是全域唯一的，因此應該包含您的名稱或縮寫：
   
    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
5. 在 [伺服器總管] 中，連接到您的 Azure 訂用帳戶，然後在您的「Azure 儲存體」帳戶中建立名為 **templates** 的容器。 在 [屬性] 面板中，將 **templates** 容器的 [公用讀取存取] 權限設定為 [Blob]。
6. 在 [伺服器總管] 中，於 [templates] 容器上按一下滑鼠右鍵，然後按一下 [檢視 Blob 容器]。 按一下 [上傳 Blob] 按鈕，選取 **parameters.json** 和 **templates.json** 這兩個檔案，然後按一下 [開啟]，將 JSON 檔案上傳至 **templates** 容器。 包含 JSON 資料的 blob 的 URL 如下：
   
    ```
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. 將下列方法新增至 Program.cs：
   
    ```
    static void CreateIoTHub(ResourceManagementClient client)
    {
   
    }
    ```
8. 將下列程式碼加入 **CreateIoTHub** 方法，以提交範本和參數檔案給 Azure Resource Manager：
   
    ```
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```
9. 將下列程式碼加入 **CreateIoTHub** 方法，以顯示新的 IoT 中樞的狀態和金鑰：
   
    ```
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);
   
    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>完成並執行應用程式
在建置和執行應用程式之前，您現在可以呼叫 **CreateIoTHub** 方法來完成應用程式。

1. 在 **Main** 方法的結尾加入下列程式碼：
   
    ```
    CreateIoTHub(client);
    Console.ReadLine();
    ```
2. 按一下 [建置]，然後按一下 [建置方案]。 更正所有錯誤。
3. 按一下 [偵錯]，然後按一下 [開始偵錯] 以執行應用程式。 可能需要數分鐘的時間，部署才會開始執行。
4. 若要確認應用程式是否新增了 IoT 中樞，可前往 [Azure 入口網站][lnk-azure-portal]檢視您的資源清單，或是使用 **Get-AzureRmResource** PowerShell Cmdlet。

> [!NOTE]
> 此範例應用程式會加入您付費的「S1 標準 IoT 中樞」。 您可透過 [Azure 入口網站][lnk-azure-portal]刪除此 IoT 中樞，或在完成時，使用 **Remove-AzureRmResource** PowerShell Cmdlet。
> 
> 

## <a name="next-steps"></a>後續步驟
現在您已經使用 Azure Resource Manager 範本和 C# 程式部署 IoT 中樞，可以進一步探索：

* 閱讀 [IoT 中樞資源提供者 REST API][lnk-rest-api] 功能的相關資訊。
* 如需 Azure Resource Manager 功能的詳細資訊，請參閱 [Azure Resource Manager 概觀][lnk-azure-rm-overview]。

若要深入了解如何開發 IoT 中樞，請參閱以下內容︰

* [C SDK 簡介][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

若要進一步探索 IoT 中樞的功能，請參閱︰

* [使用 IoT 閘道 SDK 來模擬裝置][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-storage-account]: ../storage/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Nov16_HO5-->


