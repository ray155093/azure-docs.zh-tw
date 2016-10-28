<properties 
	pageTitle="在 Azure Resource Manager 中連結資源 | Microsoft Azure" 
	description="在 Azure Resource Manager 中建立不同資源群組中相關資源之間的連結。" 
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
	ms.date="08/01/2016" 
	ms.author="tomfitz"/>

# 在「Azure 資源管理員」中連結資源

在部署期間，您可以將某個資源標示為依存於另一個資源，但在部署時結束該生命週期。部署之後，相依資源之間就沒有可識別的關聯性。Resource Manager 提供稱為「資源連結」的功能，來建立資源之間的永續性關聯性。

運用資源連結，您可以記錄跨多個資源群組的關聯性。例如，下列情況很常見：具有自己的生命週期的資料庫存在於一個資源群組中，而具有不同生命週期的應用程式存在於不同的資源群組中。應用程式會連接到資料庫，因此您想要標示應用程式與資料庫之間的連結。

所有已連結的資源都必須屬於同一個訂用帳戶。每個資源都可以連結至其他 50 個資源。查詢相關資源的唯一方法是透過 REST API。如果任何已連結的資源被刪除或移動，連結擁有者必須清除剩餘的連結。刪除連結到其他資源的資源時，您「不」會收到警告。

## 範本中的連結

若要在範本中定義連結，請包括資源類型，這個資源類型合併資源提供者命名空間和來源資源類型與 **/providers/links**。此名稱必須包含來源資源的名稱。您可以提供目標資源的資源識別碼。下列範例會建立網站與儲存體帳戶之間的連結。

    {
      "type": "Microsoft.Web/sites/providers/links",
      "apiVersion": "2015-01-01",
      "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
      "dependsOn": [ "[variables('siteName')]" ],
      "properties": {
        "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
        "notes": "This web site uses the storage account to store user information."
      }
    }


如需範本格式的完整說明，請參閱[資源連結 - 範本結構描述](resource-manager-template-links.md)。

## 使用 REST API 連結

若要定義已部署資源之間的連結，請執行：

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

將 {subscription-id} 取代成您的訂用帳戶識別碼。使用識別連結中第一個資源的值取代 {resource-group}、{provider-namespace}、{resource-type} 和 {resource-name}。以要建立之連結的名稱取代 {link-name}。對於 api-version，請使用 2015-01-01。

在要求中，包含定義連結中第二個資源的物件：

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

properties 元素包含第二個資源的識別碼。

您可以使用下列項目，查詢訂用帳戶中的連結︰

    https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Resources/links?api-version={api-version}

如需其他範例，包括如何抓取連結的相關資訊，請參閱[連結的資源](https://msdn.microsoft.com/library/azure/mt238499.aspx)。

## 後續步驟

- 您也可以使用標記來組織您的資源。若要了解如何標記資源，請參閱[使用標記來組織資源](resource-group-using-tags.md)。
- 如需如何建立範本並定義要部署之資源的說明，請參閱[撰寫範本](resource-group-authoring-templates.md)。

<!---HONumber=AcomDC_0803_2016-->