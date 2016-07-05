<properties
   pageTitle="使用入口網站來檢視部署作業 | Microsoft Azure"
   description="描述如何使用 Azure 入口網站來偵測源自「資源管理員」部署的錯誤。"
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/15/2016"
   ms.author="tomfitz"/>

# 使用 Azure 入口網站來檢視部署作業

> [AZURE.SELECTOR]
- [入口網站](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
- [REST API](resource-manager-troubleshoot-deployments-rest.md)

您可以透過 Azure 入口網站檢視部署的作業。當您在部署期間收到錯誤時，您可能對於檢視作業最感興趣，所以本文著重於檢視失敗的作業。入口網站提供介面，讓您輕鬆地找到錯誤並判斷可能的修正方法。

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## 使用部署作業進行疑難排解

若要查看部署作業，請使用下列步驟 ︰

1. 針對部署所含的資源群組，請注意最後一個部署的狀態。您可以選取此狀態，以取得更多詳細資料。

    ![deployment status](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)

2. 您會看到最近的部署歷程記錄。選取失敗的部署。

    ![deployment status](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)

3. 選取 [失敗。如需詳細資訊，請按一下這裡]，以查看部署失敗的原因描述。在下圖中，DNS 記錄不是唯一的。

    ![檢視失敗的部署](./media/resource-manager-troubleshoot-deployments-portal/view-error.png)

    此錯誤訊息應足以讓您開始進行疑難排解。不過，如果您需要有關哪些工作已完成的詳細資料，您可以檢視如下列步驟中所示的作業。

4. 您可以在 [部署] 刀鋒視窗中檢視所有的部署作業。選取任一作業以查看詳細資料。

    ![檢視作業](./media/resource-manager-troubleshoot-deployments-portal/view-operations.png)

    在此情況下，您會看到儲存體帳戶、虛擬網路和可用性設定組都已成功建立。公用 IP 位址失敗，並未嘗試其他資源。

5. 您可以選取 [事件]，以檢視部署的事件。

    ![檢視事件](./media/resource-manager-troubleshoot-deployments-portal/view-events.png)

6. 您會看見部署的所有事件，而選取任何一個事件可取得詳細資訊。

    ![查看事件](./media/resource-manager-troubleshoot-deployments-portal/see-all-events.png)

## 使用稽核記錄檔進行疑難排解

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

若要查看部署相關錯誤，請使用下列步驟 ︰

1. 選取 [稽核記錄檔]，以檢視資源群組的稽核記錄檔。

    ![選取稽核記錄檔](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)

2. 在 [稽核記錄檔] 刀鋒視窗中，您會看到訂用帳戶中所有資源群組的最新作業摘要。它會包含表示作業的時間與狀態的圖形，以及作業的清單。

    ![顯示動作](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)

3. 您可以篩選稽核記錄檔的檢視，以著重於特定條件。選取 [稽核記錄檔] 刀鋒視窗頂端的 [篩選]。

    ![篩選記錄檔](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)

4. 從 [篩選] 刀鋒視窗中，選取條件，將稽核記錄檔的檢視限制為您要查看的作業。例如，您可以篩選作業，使其只顯示此資源群組的錯誤。

    ![設定篩選選項](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)

5. 您可以設定時間範圍，進一步篩選作業。下圖會以特定的 20 分鐘時間範圍來檢視篩選。

    ![設定時間](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)

6. 您可以在清單中選取任何一個作業。選取包含您想要調查之錯誤的作業。

    ![選取作業](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
  
7. 您會看到該作業的所有事件。請注意摘要中的**相互關聯識別碼**。此識別碼可用來追蹤相關的事件。與技術支援人員合作來疑難排解問題時，這非常有用。您可以選取任何事件，以查看事件的相關詳細資料。

    ![選取事件](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)

8. 您會看到事件的相關詳細資料。請特別注意**屬性**，以取得錯誤的相關資訊。

    ![顯示稽核記錄檔詳細資料](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

您套用到稽核記錄檔的篩選會保留到您下次檢視稽核記錄檔時，因此您可能需要變更這些值，以擴大您的作業檢視。

## 後續步驟

- 如需解決特定部署錯誤的說明，請參閱[針對使用 Azure Resource Manager 將資源部署至 Azure 時常見的錯誤進行疑難排解](resource-manager-common-deployment-errors.md)。
- 若要了解如何使用稽核記錄檔來監視其他類型的動作，請參閱[使用 Resource Manager 來稽核作業](resource-group-audit.md)。
- 若要在執行之前驗證您的部署，請參閱[使用 Azure Resource Manager 範本部署資源群組](resource-group-template-deploy.md)。

<!---HONumber=AcomDC_0622_2016-->