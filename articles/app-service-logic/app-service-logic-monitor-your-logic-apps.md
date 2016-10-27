<properties 
    pageTitle="在 Azure App Service 中監視 Logic Apps | Microsoft Azure" 
    description="如何查看邏輯應用程式的完成項目" 
    authors="jeffhollan" 
    manager="erikre" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/>


# <a name="monitor-your-logic-apps"></a>監視邏輯應用程式

[建立邏輯應用程式](app-service-logic-create-a-logic-app.md)之後，您可以在 Azure 入口網站中查看其執行的完整記錄。  您也可以設定 Azure 診斷和 Azure 警示等服務來即時監視事件，並針對「當一小時內發生超過 5 次執行失敗時」之類的事件對您發出警示。

## <a name="monitor-in-the-azure-portal"></a>在 Azure 入口網站中進行監視

若要檢視記錄，請選取 [瀏覽]，然後選取 [Logic Apps]。 隨即會顯示您訂用帳戶中所有 Logic Apps 的清單。  選取您想要監視的邏輯應用程式。  您便會看到此邏輯應用程式已發生的所有動作和觸發程序的清單。

![Overview](./media/app-service-logic-monitor-your-logic-apps/overview.png)

此刀鋒視窗上有幾個很實用的區段︰

- [摘要] 會列出 [所有執行] 和 [觸發程序記錄]
    -  會列出最新的邏輯應用程式執行。  您可以按一下任何資料列以取得該次執行的詳細資料，或按一下圖格以列出更多執行。
    -  會列出此邏輯應用程式的所有觸發程序活動。  觸發程序活動可能會是「略過」的新資料檢查 (例如想要查看 FTP 是否已新增新的檔案)、「成功」(表示已傳回資料而引發邏輯應用程式) 或「失敗」(對應到設定錯誤)。
-  可讓您檢視執行階段詳細資料和事件，並訂閱 [Azure 警示](#adding-azure-alerts)

>[AZURE.NOTE] 在邏輯應用程式服務中，所有執行階段詳細資料和事件都會以靜止狀態加密。 只有在使用者要求檢視時，才會將其解密。 這些事件的存取權也可以 Azure 角色型存取控制 (RBAC) 來控制。

### <a name="view-the-run-details"></a>檢視執行的詳細資料

這份執行清單會顯示特定執行的**狀態**、**開始時間**和**持續時間**。 選取任何資料列以查看該執行的詳細資料。

監視檢視會顯示執行的每個步驟、輸入和輸出，以及可能已發生的任何錯誤訊息。

![執行和動作](./media/app-service-logic-monitor-your-logic-apps/monitor-view.png)

如果您需要其他詳細資料，例如執行的**相互關聯識別碼** (可用於 REST API)，您可以按一下 [執行詳細資料] 按鈕。  這包括執行的所有步驟、狀態和輸入/輸出。

## <a name="azure-diagnostics-and-alerts"></a>Azure 診斷和警示

除了 Azure 入口網站和 REST API 所提供的上述詳細資料外，您還可以設定邏輯應用程式，使用 Azure 診斷來取得更豐富的詳細資料和偵錯。

1. 按一下邏輯應用程式刀鋒視窗的 [診斷]  區段
1. 按一下以設定 [診斷設定] 
1. 設定用來發出資料的目的地事件中樞或儲存體帳戶

    ![Azure 診斷設定](./media/app-service-logic-monitor-your-logic-apps/diagnostics.png)

### <a name="adding-azure-alerts"></a>新增 Azure 警示

設定了診斷之後，您可以新增要在超過特定臨界值時引發的 Azure 警示。  在 [診斷] 刀鋒視窗中，選取 [警示] 圖格和 [新增警示]。  這將會引導您設定以臨界值和度量的數字為基礎的警示。

![Azure 警示度量](./media/app-service-logic-monitor-your-logic-apps/alerts.png)

您可以設定所需的**條件**、**臨界值**和**期間**。  最後，您可以設定用來傳送通知的目的地電子郵件地址，或設定 Webhook。  您也可以在邏輯應用程式中使用要對警示執行的[要求觸發程序](../connectors/connectors-native-reqres.md) (以執行[張貼至 Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)、[傳送簡訊](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)或[將訊息新增至佇列](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)等動作)。

### <a name="azure-diagnostics-settings"></a>Azure 診斷設定

這些事件各包含有關邏輯應用程式和事件 (例如狀態) 的詳細資料。  以下是「ActionCompleted」  事件的範例︰

```javascript
{
            "time": "2016-07-09T17:09:54.4773148Z",
            "workflowId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
            "resourceId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-06-01",
                "startTime": "2016-07-09T17:09:53.4336305Z",
                "endTime": "2016-07-09T17:09:53.5430281Z",
                "status": "Succeeded",
                "code": "OK",
                "resource": {
                    "subscriptionId": "80d4fe69-ABCD-EFGH-a938-9250f1c8ab03",
                    "resourceGroupName": "MyResourceGroup",
                    "workflowId": "cff00d5458f944d5a766f2f9ad142553",
                    "workflowName": "MyLogicApp",
                    "runId": "08587361146922712057",
                    "location": "eastus",
                    "actionName": "Http"
                },
                "correlation": {
                    "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
                    "clientTrackingId": "my-custom-tracking-id"
                },
                "trackedProperties": {
                    "myProperty": "<value>"
                }
            }
        }
```

特別適合用於追蹤和監視的兩個屬性分別是 *clientTrackingId* 和 *trackedProperties*。  

#### <a name="client-tracking-id"></a>用戶端追蹤識別碼

用戶端追蹤識別碼是會跨邏輯應用程式執行來將事件相互關聯的值，包括邏輯應用程式執行過程中所呼叫的任何巢狀工作流程。  這個識別碼會自動產生 (如果未提供)，但您可以藉由在觸發程序要求 (要求觸發程序、HTTP 觸發程序或 Webhook 觸發程序) 中傳遞 `x-ms-client-tracking-id` 標頭與識別碼值，從觸發程序手動指定用戶端追蹤識別碼。

#### <a name="tracked-properties"></a>追蹤的屬性

追蹤的屬性可以新增到工作流程定義中的動作，來追蹤診斷資料中的輸入或輸出。  如果您想要追蹤遙測中的「訂單識別碼」之類的資料，這會非常實用。  若要新增追蹤的屬性，請在動作中加入 `trackedProperties` 屬性。  追蹤的屬性只能追蹤單一動作的輸入和輸出，但您可以使用事件的 `correlation` 屬性來跨執行中的動作進行相互關聯。

```javascript
{
    "myAction": {
        "type": "http",
        "inputs": {
            "uri": "http://uri",
            "headers": {
                "Content-Type": "application/json"
            },
            "body": "@triggerBody()"
        },
        "trackedProperties":{
            "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
            "myActionHTTPValue": "@action()['outputs']['body']['foo']",
            "transactionId": "@action()['inputs']['body']['bar']"
        }
    }
}
```

### <a name="extending-your-solutions"></a>延伸您的方案

您可以從事件中樞或儲存體將這項遙測利用到其他服務，例如 [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite)、[Azure 串流分析](https://azure.microsoft.com/services/stream-analytics/)和 [Power BI](https://powerbi.com)，以便即時監視您的整合工作流程。

## <a name="next-steps"></a>後續步驟
- [Logic Apps 範例和常見案例](app-service-logic-examples-and-scenarios.md)
- [建立邏輯應用程式部署範本](app-service-logic-create-deploy-template.md)
- [企業整合功能](app-service-logic-enterprise-integration-overview.md)



<!--HONumber=Oct16_HO2-->


