---
title: "使用 Azure 診斷收集記錄檔 | Microsoft Docs"
description: "本文將說明如何設定 Azure 診斷從 Azure 中執行的 Service Fabric 叢集收集記錄檔"
services: service-fabric
documentationcenter: .net
author: ms-toddabel
manager: timlt
editor: 
ms.assetid: 9f7e1fa5-6543-4efd-b53f-39510f18df56
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2016
ms.author: toddabel
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 454e1379e4ec598ffd31017b413f6b15c98039a6


---
# <a name="collect-logs-by-using-azure-diagnostics"></a>使用 Azure 診斷收集記錄檔
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
> * [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
> 
> 

當您執行 Azure Service Fabric 叢集時，最好從中央位置的所有節點收集記錄檔。 將記錄檔集中在中央位置，可協助您分析並針對叢集或該叢集中執行之應用程式與服務的問題進行疑難排解。

上傳和收集記錄檔的方式之一是使用可將記錄檔上傳至 Azure 儲存體的 Azure 診斷擴充功能。 這些記錄檔實際上在儲存體中並不那麼實用。 但您可以使用外部處理序來讀取儲存體的事件，並將它們放在 [Log Analytics](../log-analytics/log-analytics-service-fabric.md)、[Elastic Search](service-fabric-diagnostic-how-to-use-elasticsearch.md) 之類的產品或其他記錄檔剖析解決方案中。

## <a name="prerequisites"></a>必要條件
您將使用這些工具來執行這份文件中的某些作業：

* [Azure 診斷](../cloud-services/cloud-services-dotnet-diagnostics.md) (與 Azure 雲端服務相關，但具備有用的資訊和範例)
* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](../powershell-install-configure.md)
* [Azure Resource Manager 用戶端](https://github.com/projectkudu/ARMClient)
* [Azure Resource Manager 範本](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="log-sources-that-you-might-want-to-collect"></a>您可能想要收集的記錄來源
* **Service Fabric 記錄檔**：由平台發出到標準 Windows 事件追蹤 (ETW) 和 EventSource 通道。 記錄檔可以是下列其中一種類型：
  * 操作事件：Service Fabric 平台所執行之作業的記錄檔。 範例包括建立應用程式和服務、節點狀態變更和升級資訊。
  * [Reliable Actors 程式設計模型事件](service-fabric-reliable-actors-diagnostics.md)
  * [Reliable Services 程式設計模型事件](service-fabric-reliable-services-diagnostics.md)
* **應用程式事件**：從您的服務程式碼發出且使用 Visual Studio 範本所提供的 EventSource 協助程式類別所寫出的事件。 如需有關如何從應用程式寫入記錄檔的詳細資訊，請參閱[監視和診斷本機開發設定中的服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)。

## <a name="deploy-the-diagnostics-extension"></a>部署診斷擴充功能
收集記錄檔的第一個步驟是將診斷擴充功能部署在 Service Fabric 叢集的每個 WM 上。 診斷擴充功能會收集每個 VM 上的記錄檔，並將它們上傳至您指定的儲存體帳戶。 步驟視您使用 Azure 入口網站或 Azure Resource Manager 而稍微有所不同。 步驟也會視部署為叢集建立的一部分，或是針對現有的叢集而有所不同。 讓我們看看每個案例的步驟。

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-the-portal"></a>透過入口網站建立叢集時部署診斷擴充功能
為了在建立叢集時將診斷擴充功能部署至叢集中的 WM，您會使用下圖所示的診斷設定面板。 若要收集 Reliable Actors 或 Reliable Services 事件，請確定已將 [診斷] 設定為 [開啟] (這是預設設定)。 建立叢集之後，您就無法使用入口網站變更這些設定。

![入口網站中用於建立叢集的 Azure 診斷設定](./media/service-fabric-diagnostics-how-to-setup-wad/portal-cluster-creation-diagnostics-setting.png)

Azure 支援團隊「需要」支援記錄檔，才能盡心處理您所建立的任何支援要求。 這些記錄檔會即時收集，並儲存在建立於資源群組中的其中一個儲存體帳戶。 [診斷] 設定可設定應用程式層級的事件。 這些事件包括會儲存在 Azure 儲存體的 [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) 事件、[Reliable Services](service-fabric-reliable-services-diagnostics.md) 事件，以及部分系統層級 Service Fabric 事件。

[Elastic Search](service-fabric-diagnostic-how-to-use-elasticsearch.md) 等產品或您自己的處理序可以從儲存體帳戶中取得事件。 目前沒有任何方法可以篩選或清理已傳送至資料表的事件。 如果不實作從資料表移除事件的處理序，資料表將會繼續成長。

使用入口網站建立叢集時，強烈建議您先下載範本，「再按一下 [確定]**」來建立叢集。 如需詳細資訊，請參閱[使用 Azure Resource Manager 範本來設定 Service Fabric 叢集](service-fabric-cluster-creation-via-arm.md)。 您需要範本以在稍後進行變更，因為您無法使用入口網站進行某些變更。

您可以使用下列步驟從入口網站匯出範本。 不過，這些範本的使用方式可能會比較困難，因為它們可能包含缺少必要資訊的 null 值。

1. 開啟您的資源群組。
2. 選取 [設定] 以顯示設定面板。
3. 選取 [部署] 以顯示部署歷程記錄面板。
4. 選取一個部署以顯示該部署的詳細資料。
5. 選取 [匯出範本] 以顯示範本面板。
6. 選取 [儲存至檔案] 以匯出包含範本、參數和 PowerShell 檔案的 .zip 檔案。

匯出檔案之後，您需要進行修改。 編輯 parameters.json 檔案，並移除 **adminPassword** 元素。 執行部署指令碼時，這樣會導致密碼的提示。 在執行部署指令碼時，您可能必須修正 null 參數值。

使用下載的範本來更新組態：

1. 將內容解壓縮到本機電腦上的資料夾。
2. 修改內容以反映新的組態。
3. 啟動 PowerShell 並變更到您要解壓縮內容的資料夾。
4. 執行 **deploy.ps1** 並填入訂用帳戶識別碼、資源群組名稱 (使用相同的名稱來更新組態) 和唯一的部署名稱。

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>使用 Azure Resource Manager 在建立叢集時部署診斷擴充功能
若要使用 Resource Manager 建立叢集，您需要在建立叢集之前，將診斷組態 JSON 加入至完整的叢集 Resource Manager 範本。 我們在 Resource Manager 範本範例中提供一個五 VM 叢集 Resource Manager 範本，且已在其中加入診斷設定。 您可以在 Azure 資源庫中的這個位置看到它： [具有診斷 Resource Manager 範本範例的五節點叢集](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad)。

若要查看 Resource Manager 範本中的 [診斷] 設定，請開啟 azuredeploy.json 檔案，並搜尋 **IaaSDiagnostics**。 若要使用這個範本建立叢集，請選取上一個連結所提供的 [部署到 Azure] 按鈕。

或者，您也可以下載資源管理員範例，對它進行變更，然後在 Azure PowerShell 視窗中使用 `New-AzureRmResourceGroupDeployment` 命令來使用修改過的範本建立叢集。 針對您傳遞給命令的參數，請參閱以下程式碼。 如需如何使用 PowerShell 部署資源群組的詳細資訊，請參閱[使用 Azure Resource Manager 範本部署資源群組](../resource-group-template-deploy.md)。

```powershell

New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>將診斷擴充功能部署到現有的叢集
如果您具有未部署診斷的現有叢集，或是想要修改現有的組態，您可以使用下列步驟來新增或更新它。 修改用來建立現有叢集的 Resource Manager 範本，或是以上述方式從入口網站下載範本。 執行下列工作來修改 template.json 檔案。

藉由新增儲存體資源到資源區段，以將其新增至範本。

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 接下來，新增至參數區段中儲存體帳戶定義之後的位置，位於 `supportLogStorageAccountName` 和 `vmNodeType0Name` 之間。 以儲存體帳戶的名稱取代預留位置文字 *storage account name goes here*。

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
然後，透過在擴充功能陣列內新增下列內容，更新 template.json 檔案的 `VirtualMachineProfile` 區段。 請務必在開頭或結尾 (取決於其插入的位置) 加入逗點。

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

如所述修改 template.json 檔案之後，將 Resource Manager 範本重新發佈。 如果已匯出範本，執行 deploy.ps1 檔案將會重新發佈範本。 部署之後，請確認 **ProvisioningState** 為 **Succeeded**。

## <a name="update-diagnostics-to-collect-and-upload-logs-from-new-eventsource-channels"></a>更新診斷從新的 EventSource 通道收集並上傳記錄檔
若要更新診斷以從新的 EventSource 通道 (代表您將要部署的新應用程式) 收集記錄檔，請執行[上一節](#deploywadarm)中相同的步驟，以針對現有叢集設定診斷。

在您使用 `New-AzureRmResourceGroupDeployment` PowerShell 命令套用組態更新之前，請先更新 template.json 檔案中的 `EtwEventSourceProviderConfiguration` 區段，以新增 EventSource 通道的項目。 在 Visual Studio 產生的 ServiceEventSource.cs 檔案中，事件來源的名稱定義為程式碼的一部分。

例如，如果您的事件資源名稱是 My-Eventsource，新增下列程式碼以將來自 My-Eventsource 的事件置於名稱為 MyDestinationTableName 的資料表中。

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

若要收集效能計數器或事件記錄檔，請以[使用 Azure Resource Manager 範本建立具有監視和診斷的 Windows 虛擬機器](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中提供的範例來修改 Resource Manager 範本。 然後請重新發佈 Resource Manager 範本。

## <a name="next-steps"></a>後續步驟
若要更詳細了解進行問題移難排解時應該注意的事件，請查看針對 [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) 和 [Reliable Services](service-fabric-reliable-services-diagnostics.md) 所發出的診斷事件。

## <a name="related-articles"></a>相關文章
* [了解如何使用診斷擴充功能收集效能計數器或記錄檔](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Log Analytics 中的 Service Fabric 解決方案](../log-analytics/log-analytics-service-fabric.md)




<!--HONumber=Nov16_HO3-->


