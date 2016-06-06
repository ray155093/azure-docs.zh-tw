<properties
	pageTitle="自動調整與虛擬機器擴展集 | Microsoft Azure"
	description="深入了解使用診斷和自動調整資源以自動調整擴展集中的虛擬機器。"
	services="virtual-machine-scale-sets"
    documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="davidmu"/>

# 自動調整與虛擬機器擴展集

擴展集內的虛擬機器的自動調整是依應用程式的需求建立或刪除集合中的機器以符合效能需求，並滿足服務等級協定 (SLA)。當工作量增長時，應用程式可能需要額外的資源，才能有效執行工作。

自動調整是自動化程序，可以透過持續監視系統效能，以及決定新增或移除不需要的資源，協助簡化管理負擔。調整是彈性程序。在系統上的負載增加時可以佈建較多的資源，但是可以解除配置以隨選減少資源，將成本降至最低，同時仍然可以維持適當的效能，並符合 SLA。

使用 Azure Resource Manager 範本在擴展集上設定自動調整，方法是使用 Azure PowerShell 或使用 Azure CLI。

## 使用 Resource Manager 範本，設定調整

您不是分開部署與管理應用程式的每個資源，而是使用範本，藉此經由協調的單一作業來部署與佈建所有資源。在範本中，會定義應用程式資源，且會針對不同的環境指定部署參數。範本由 JSON 與運算式所組成，可讓您用來為部署建構值。若要深入了解，請參閱[編寫 Azure Resource Manager 範本](../resource-group-authoring-templates.md)。

在範本中，您可以指定容量元素︰

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 3
    },

容量會識別集合中的虛擬機器數目。您可以使用不同值部署範本，手動變更容量。如果您部署範本只是要變更容量，則僅包含具有更新容量的 SKU 元素。

自動變更您的擴展集的容量，方法是使用 Azure Resource Manager 提供的 autoscaleSettings 資源，和擴展集中的機器上安裝的 Azure 診斷擴充的組合。

### 設定 Azure 診斷擴充

自動調整只能夠在擴展集中的每個虛擬機器上的度量集合成功時完成。Azure 診斷擴充提供監視和診斷功能，符合自動調整資源的度量集合需求。您可以安裝擴充做為 Resource Manager 範本的一部分。[使用 Azure Resource Manager 範本建立具有監控和診斷功能的 Windows 虛擬機器](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)提供關於使用擴充的詳細資訊。

此範例會顯示在範本中用來設定診斷擴充的變數︰

	"diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
	"accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
	"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB="4096" xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/> <WindowsEventLog scheduledTransferPeriod="PT1M" > <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="Security!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="System!*[System[(Level = 1 or Level = 2)]]" /></WindowsEventLog>",
	"wadperfcounter": "<PerformanceCounters scheduledTransferPeriod="PT1M"><PerformanceCounterConfiguration counterSpecifier="\\Processor(_Total)\\Thread Count" sampleRate="PT15S" unit="Percent"><annotation displayName="Thread Count" locale="zh-TW"/></PerformanceCounterConfiguration>",
	"wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId="')]",
	"wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
	"wadcfgxend": "[concat('"><MetricAggregation scheduledTransferPeriod="PT1H"/><MetricAggregation scheduledTransferPeriod="PT1M"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

針對如儲存體帳戶 (在其中儲存資料，並且從其中的資料收集擴展集的名稱) 的名稱的值部署範本時，會提供參數。另外在此 Windows Server 範例中，只會收集「執行緒計數」效能計數器，但是 Windows 或 Linux 中所有可用的效能計數器可以用來收集診斷資訊，並且可以包含在擴充組態中。

此範例會顯示範本中擴充的定義︰

    "extensionProfile": {
      "extensions": [
        {
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
              "storageAccount": "[variables('diagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
              "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
              "storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
              "storageAccountEndPoint": "https://core.windows.net"
            }
          }
        }
      ]
    }

診斷擴充執行時，會收集資料表中的資料，該資料表位於您指定的儲存體帳戶中。在 WADPerformanceCounters 資料表中，您可以找到收集的資料。例如，這是當第一次在擴展集中建立虛擬機器時所收集的執行緒計數：

![新增負載之前的 Windows Server 效能計數器資料的螢幕擷取畫面](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### 設定 autoScaleSettings 資源

autoscaleSettings 資源會使用診斷擴充所收集的資訊，以進行有關如何在擴展集中增加或減少虛擬機器數目的決策。

此範例會顯示範本中資源的組態︰

    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "[concat(parameters('resourcePrefix'),'as1')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      ],
      "properties": {
        "enabled": true,
        "name": "[concat(parameters('resourcePrefix'),'as1')]",
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 650
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 550
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ],
        "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
      }
    }

在上述範例中，兩個規則用於定義自動調整動作。第一個規則定義相應放大動作，第二個規則定義相應縮小動作。在規則中提供這些值︰

- **metricName** - 這與您在診斷擴充的 wadperfcounter 變數中定義的效能計數器相同。在上述範例中，會使用「執行緒計數」計數器。  
- **metricResourceUri** - 這是虛擬機器調整集的資源識別碼。這個識別碼包含資源群組的名稱、資源提供者的名稱和要調整的擴展集的名稱。
- **timeGrain** – 這是所收集之計量的精細度。在上述範例中，資料是以一分鐘的間隔進行收集。此值與 timeWindow 結合使用。
- **statistic** – 這會決定如何結合計量以因應自動調整動作的需要。可能的值為：Average、Min、Max。
- **timeWindow** – 這是收集執行個體資料的時間範圍。其值必須介於 5 分鐘到 12 小時之間。
- **timeAggregation** – 這會決定收集的資料應如何隨著時間結合。預設值為 Average。可能的值為：Average、Minimum、Maximum、Last、Total、Count。
- **operator** – 這是用來比較計量資料和臨界值的運算子。可能的值為：Equals、NotEquals、GreaterThan、GreaterThanOrEqual、LessThan、LessThanOrEqual。
- **threshold** – 這是觸發調整動作的值。您必須確定提供您為相應放大動作所設定的臨界值與您為相應縮小動作所設定的臨界值之間足夠的差異。如果您設定的值相同，例如在上述範例中均設定為 600 個執行緒，系統就會預期集合大小的常數增加和減少，並且不會實作調整動作。
- **direction** – 這會決定達到臨界值時所採取的動作。可能的值為 Increase 或 Decrease。
- **type** – 這是所應採取的動作類型，必須設為 ChangeCount。
- **value** – 這是在擴展集內新增或移除的虛擬機器數目。此值必須是 1 或更大。
- **cooldown** – 這是在上一個調整動作之後、下一個動作執行之前的等待時間量。其值必須介於一分鐘到一週之間。

根據您使用的效能計數器，會以不同的方式使用範本組態中的某些元素。在所示範例中，使用的效能計數器是「執行緒計數」，且臨界值針對相應放大動作設定為 650，針對相應縮小動作設定為 550。如果您使用如 %Processor Time 的計數器，臨界值會設定為 CPU 使用量的百分比，判斷調整動作。

當集合中的虛擬機器上所建立的負載觸發調整動作時，集合中的機器數目會根據範本中的值元素而增加。例如，在容量設為 3 且調整動作值設為 1 的擴展集中：

![自動調整之前的集合中機器數目的螢幕擷取畫面](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

當建立的負載導致平均執行緒計數超過 650 的臨界值時︰

![新增負載之後的 Windows Server 效能計數器資料的螢幕擷取畫面](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

會觸發相應放大動作，讓集合的容量增加 1：

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 4
    },

且虛擬機器會新增至擴展集：

![自動調整之後的集合中機器數目的螢幕擷取畫面](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

經過五分鐘的冷卻期間之後，如果機器上的執行緒平均數目仍然超過 600，則會將另一部機器新增至集合。如果平均執行緒計數保持在 550 以下，則擴展集的容量會減少 1，且機器會從集合中移除。

## 調查調整動作

- [Azure 入口網站](https://portal.azure.com/) - 目前，使用入口網站可以取得限定數量的資訊。
- [Azure 資源總管](https://resources.azure.com/) - 這是最適合用來瀏覽擴展集現行狀態的工具。
- [Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/) - 如 **Get-AzureRmResource** 或 **Get-Autoscalesetting** 的 Cmdlet 可以用來取得您的擴展集的相關資訊。
- [Azure CLI](../xplat-cli-azure-resource-manager.md) -使用 **azure resource show** 命令，取得您的集合的相關資訊。
- 比照任何其他機器連接到 Jumpbox 虛擬機器，您即可從遠端存取調整集內的虛擬機器，以監視個別程序。

## 後續步驟

1. 使用[建立 Windows 虛擬機器擴展集](virtual-machine-scale-sets-windows-create.md)中的資訊，開始建立您的第一個擴展集。
2. 請參閱[自動調整 Windows 虛擬機器擴展集](virtual-machine-scale-sets-windows-autoscale.md)或[自動調整 Linux 虛擬機器擴展集](virtual-machine-scale-sets-linux-autoscale.md)，以查看如何建立設定自動調整的擴展集。

<!---HONumber=AcomDC_0525_2016-->