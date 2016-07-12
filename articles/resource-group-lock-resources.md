<properties 
	pageTitle="使用資源管理員鎖定資源 | Microsoft Azure" 
	description="透過套用限制到所有使用者和角色，防止使用者更新或刪除特定資源。" 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/26/2016" 
	ms.author="tomfitz"/>

# 使用 Azure 資源管理員來鎖定資源

身為系統管理員，您可能需要鎖定訂用帳戶、資源群組或資源，以防止組織中的其他使用者不小心刪除或修改重要資源。您可以將鎖定層級設定為 **CanNotDelete** 或 **ReadOnly**。

- **CanNotDelete** 表示經過授權的使用者仍然可以讀取和修改資源，但無法刪除資源。
- **ReadOnly** 表示經過授權的使用者可以讀取資源，但是無法刪除資源或對其執行任何動作。對資源的權限限制為**讀取者**角色。套用 **ReadOnly** 會導致無法預期的結果，因為有些看似讀取作業的作業實際上需要其他動作。例如，將 **ReadOnly** 鎖定放置在儲存體帳戶上，會防止所有使用者列出金鑰。清單金鑰作業是透過 POST 要求進行處理，因為傳回的金鑰可用於寫入作業。至於其他範例，將 **ReadOnly** 鎖定放置在 App Service 資源，會防止 Visual Studio 伺服器總管顯示資源的檔案，因為該互動需要寫入存取權。

不同於角色型存取控制，您可以使用管理鎖定來對所有使用者和角色套用限制。如要了解使用者和角色的設定權限，請參閱 [Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。

當您在父範圍套用鎖定時，所有子系資源都會都繼承相同的鎖定。繼承中限制最嚴格的鎖定優先順序最高。

## 誰可以建立或刪除您的組織中的鎖定

若要建立或刪除管理鎖定，您必須擁有 **Microsoft.Authorization/*** 或 **Microsoft.Authorization/locks/*** 動作的存取權。內建角色中，只有**擁有者**和**使用者存取系統管理員**被授與這些動作。

## 透過入口網站建立鎖定

1. 在您想要鎖定之資源、資源群組或訂用帳戶的 [設定] 刀鋒視窗中，選取 [鎖定]。

      ![選取鎖定](./media/resource-group-lock-resources/select-lock.png)

2. 若要新增鎖定，請選取 [新增]。如果您想要改為在目前所選資源將繼承的父層級建立鎖定，請選取父系 (例如下面顯示的訂用帳戶)。

      ![新增鎖定](./media/resource-group-lock-resources/add-lock.png)

3. 提供鎖定的名稱和鎖定層級。您可以選擇性新增說明為何需要鎖定的附註。

      ![設定鎖定](./media/resource-group-lock-resources/set-lock.png)

4. 若要刪除鎖定，請從可用的選項中選取省略符號和 [刪除]。

      ![刪除鎖定](./media/resource-group-lock-resources/delete-lock.png)

## 在範本中建立鎖定

以下範例顯示的範本會在儲存體帳戶建立鎖定。要套用鎖定的儲存體帳戶會提供作為參數。鎖定名稱的建立方式是串連資源名稱與 **/Microsoft.Authorization/**，而在此案例中，鎖定名稱為 **myLock**。

必須依資源型別提供型別。針對儲存體，此型別為 "Microsoft.Storage/storageaccounts/providers/locks"。

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "lockedResource": {
          "type": "string"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
          "type": "Microsoft.Storage/storageAccounts/providers/locks",
          "apiVersion": "2015-01-01",
          "properties": {
            "level": "CannotDelete"
          }
        }
      ]
    }

## 使用 REST API 建立鎖定

您可以使用[管理鎖定的 REST API](https://msdn.microsoft.com/library/azure/mt204563.aspx)，來鎖定已部署的資源。此 REST API 可讓您建立及刪除鎖定，以及抓取現有鎖定的相關資訊。

若要建立鎖定，請執行：

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

範圍可以是訂用帳戶、資源群組或資源。lock-name 是您想要命名鎖定的任何名稱。對於 api-version，請使用 **2015-01-01**。

在要求中，包含指定鎖定屬性的 JSON 物件。

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

如需範例，請參閱[管理鎖定的 REST API](https://msdn.microsoft.com/library/azure/mt204563.aspx)。

## 使用 Azure PowerShell 建立鎖定

您可以使用 **New-AzureRmResourceLock**，來利用 Azure PowerShell 鎖定已部署的資源，如下所示。

    New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup

Azure PowerShell 為使用中的鎖定提供其他命令，例如可更新鎖定的 **Set-AzureRmResourceLock**，以及可刪除鎖定的 **Remove-AzureRmResourceLock**。

## 後續步驟

- 的如需使用資源鎖定的詳細資訊，請參閱[鎖定您的 Azure 資源](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
- 若要了解如何邏輯地組織您的資源，請參閱[使用標記來組織您的資源](resource-group-using-tags.md)。
- 若要變更資源所在的資源群組，請參閱[將資源移動到新的資源群組](resource-group-move-resources.md)
- 您可以使用自訂原則，在訂用帳戶內套用限制和慣例。如需詳細資訊，請參閱[使用原則來管理資源和控制存取](resource-manager-policy.md)。

<!---HONumber=AcomDC_0629_2016-->