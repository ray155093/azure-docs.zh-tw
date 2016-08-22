<properties
	pageTitle="Azure Insights：針對 VM 擴展集使用 Resource Manager 範本進行進階自動調整設定 | Microsoft Azure"
	description="根據多個條件和設定檔為基礎針對 VM 擴展集設定自動調整，以及適用於調整動作的電子郵件和 webhook 通知。"
	authors="kamathashwin"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="ashwink"/>

# 針對 VM 擴展集使用 Resource Manager 範本進行進階自動調整設定

您可以根據效能標準臨界值、循環排程或特定日期，針對虛擬機器擴展集 (VMSS) 進行相應放大和縮小。您也可以針對調整動作設定電子郵件和 webhook 通知。本逐步解說將會示範在 VM 擴展集上使用 Resource Manager 範本設定上述項目的範例。

>[AZURE.NOTE] 雖然本逐步解說是解釋 VM 擴展集的步驟，您也可以將它套用到自動調整的雲端服務和 Web Apps 上。如果您想要在 VM 擴展集上根據簡單的效能標準 (例如 CPU) 取得簡單的相應縮小/放大設定，請參閱 [Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) 和 [Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md) 文件



## 逐步解說
在本逐步解說中，我們會使用 [Azure Resource Explorer (Azure 資源總管)](https://resources.azure.com/) 來設定並更新 VMSS 的自動調整設定。Azure 資源總管是透過 Resource Manager 範本輕鬆管理 Azure 資源的方式。如果您還不熟悉 Azure 資源總管、工具，請參閱[本簡介](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/)。

1. 使用基本自動調整設定部署新的 VMSS。這篇文章會使用 Azure 快速入門資源庫中擁有基本自動調整範本的 Windows VMSS 設定。Linux VMSS 執行個體也適用相同的方式。

2. 建立 VMSS 之後，請透過 Azure 資源總管瀏覽到 VMSS 資源。您將會在 Microsoft.Insights 節點下看見下列內容。

	![Azure Explorer](./media/insights-advanced-autoscale-vmss/azure_explorer_navigate.png)

	範本的執行已建立了具有名稱為 **'autoscalewad'** 的預設自動調整設定。您可以在右手邊檢視此自動調整設定的完整定義。在此情況下，預設自動調整設定具有以 CPU% 為基礎的相應放大和相應縮小規則。

3. 您現在可以根據排程或特定需求新增更多設定檔和規則。我們會建立具有 3 個設定檔的自動調整設定。若要了解自動調整中的設定檔和規則，請檢閱[自動調整最佳做法](./insights-autoscale-best-practices.md)。

    | 設定檔與規則 | 說明 |
	|---------|-------------------------------------|
	| **設定檔** | **以效能/計量為基礎** |
	| 規則 | 服務匯流排佇列訊息計數 > x |
	| 規則 | 服務匯流排佇列訊息計數 < y |
	| 規則 | CPU% < n |
	| 規則 | CPU% < p |
	| **設定檔** | **工作日早上時間 (無規則)** |
	| **設定檔** | **產品發行日 (無規則)** |

4. 基於本逐步解說之用途，我們會使用下列虛構的調整案例。
	- **以負載為基礎** - 我想要根據裝載在 VMSS 上應用程式的負載進行相應放大或縮小。
	- **訊息佇列大小** - 我針對應用程式的傳入訊息使用服務匯流排佇列。我使用佇列的訊息計數和 CPU% 來設定預設設定檔，以在訊息計數或 CPU 達到臨界值時觸發調整動作。
	- **每週和每日時間** - 我想要一個以每週週期性的「每日時間」為基礎，稱為「工作日早上時間」的設定檔。根據歷史資料，我明白這段期間應該要有特定數量的 VM 執行個體來處理應用程式的負載。
	- **特殊日期** - 我已新增「產品發行日」設定檔。我會預先針對特定日期作出計畫，好讓應用程式可以準備好處理因應行銷公告，或是當我們將新產品置入應用程式時所導致的負載。
	- 最後的兩個設定檔也可以具有其他以效能標準為基礎的規則，但我決定不那麼做，而是依賴以效能標準為基礎的預設規則。針對週期性和日期式設定檔的規則為選擇性。

	自動調整引擎針對設定檔和規則的優先順序，也已在[自動調整最佳做法](insights-autoscale-best-practices.md)一文中說明。如需自動調整的常見計量清單，請參閱[自動調整的常用計量](insights-autoscale-common-metrics.md)

5. 請確定您已處於資源總管的 [讀寫] 模式

	![Autoscalewad, default autoscale setting, 自動調整, 預設自動調整設定](./media/insights-advanced-autoscale-vmss/autoscalewad.png)

6. 按一下 [編輯]。將自動調整設定中的 'profiles' 元素以下列內容「取代」：

	![設定檔](./media/insights-advanced-autoscale-vmss/profiles.png)

	```
	{
	        "name": "Perf_Based_Scale",
	        "capacity": {
	          "minimum": "2",
	          "maximum": "12",
	          "default": "2"
	        },
	        "rules": [
	          {
	            "metricTrigger": {
	              "metricName": "MessageCount",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT5M",
	              "timeAggregation": "Average",
	              "operator": "GreaterThan",
	              "threshold": 10
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
	              "metricName": "MessageCount",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT5M",
	              "timeAggregation": "Average",
	              "operator": "LessThan",
	              "threshold": 3
	            },
	            "scaleAction": {
	              "direction": "Decrease",
	              "type": "ChangeCount",
	              "value": "1",
	              "cooldown": "PT5M"
	            }
	          },
	          {
	            "metricTrigger": {
	              "metricName": "\\Processor(_Total)\\% Processor Time",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT30M",
	              "timeAggregation": "Average",
	              "operator": "GreaterThan",
	              "threshold": 85
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
	              "metricName": "\\Processor(_Total)\\% Processor Time",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT30M",
	              "timeAggregation": "Average",
	              "operator": "LessThan",
	              "threshold": 60
	            },
	            "scaleAction": {
	              "direction": "Increase",
	              "type": "ChangeCount",
	              "value": "1",
	              "cooldown": "PT5M"
	            }
	          }
	        ]
	      },
	      {
	        "name": "Weekday_Morning_Hours_Scale",
	        "capacity": {
	          "minimum": "4",
	          "maximum": "12",
	          "default": "4"
	        },
	        "rules": [],
	        "recurrence": {
	          "frequency": "Week",
	          "schedule": {
	            "timeZone": "Pacific Standard Time",
	            "days": [
	              "Monday",
	              "Tuesday",
	              "Wednesday",
	              "Thursday",
	              "Friday"
	            ],
	            "hours": [
	              6
	            ],
	            "minutes": [
	              0
	            ]
	          }
	        }
	      },
	      {
	        "name": "Product_Launch_Day",
	        "capacity": {
	          "minimum": "6",
	          "maximum": "20",
	          "default": "6"
	        },
	        "rules": [],
	        "fixedDate": {
	          "timeZone": "Pacific Standard Time",
	          "start": "2016-06-20T00:06:00Z",
	          "end": "2016-06-21T23:59:00Z"
	        }
	      }
	```
	如需支援的欄位和其值，請參閱[自動調整 REST API 文件](https://msdn.microsoft.com/zh-TW/library/azure/dn931928.aspx)。

	現在，您的自動調整設定已包含上述的 3 個設定檔。

7. 	我們最後來查看一下自動調整的「通知」區段。自動調整通知能讓您在相應放大或縮小動作成功觸發時執行三件事。

	1. 通知系統管理員和共同管理員有關您訂用帳戶的狀況

	2. 傳送電子郵件給一組使用者

	3. 觸發 webhook 呼叫。引發時，此 webhook 將會傳送有關自動調整狀態和 VMSS 資源的中繼資料。若要深入了解自動調整 webhook 的承載，請參閱[針對自動調整設定 Webhook 與電子郵件通知](./insights-autoscale-to-webhook-email.md)。

	新增下列內容到自動調整設定，並取代其值為 null 的 **notification** 元素

	```
	"notifications": [
	      {
	        "operation": "Scale",
	        "email": {
	          "sendToSubscriptionAdministrator": true,
	          "sendToSubscriptionCoAdministrators": false,
	          "customEmails": [
	              "user1@mycompany.com",
	              "user2@mycompany.com"
	              ]
	        },
	        "webhooks": [
	          {
	            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
	            "properties": {
	              "optional_key1": "optional_value1",
	              "optional_key2": "optional_value2"
	            }
	          }
	        ]
	      }
	    ]

	```

	按一下資源總管中的 [放置] 按鈕以更新自動調整設定。

您已將 VM 擴展集上的自動調整設定更新為包含多個調整設定檔和調整通知。

## 後續步驟

請使用下列連結來深入了解自動調整。

[自動調整的常用計量](./insights-autoscale-common-metrics.md)

[Azure 自動調整的最佳作法](./insights-autoscale-best-practices.md)

[使用 PowerShell 管理自動調整](./insights-powershell-samples.md#create-and-manage-autoscale-settings)

[使用 CLI 管理自動調整](./insights-cli-samples.md#autoscale)

[針對自動調整設定 Webhook 與電子郵件通知](./insights-autoscale-to-webhook-email.md)

<!---HONumber=AcomDC_0810_2016-->