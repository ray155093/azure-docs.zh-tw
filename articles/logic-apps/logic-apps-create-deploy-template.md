---
title: "建立邏輯應用程式部署範本 | Microsoft Docs"
description: "了解如何建立邏輯應用程式部署範本，並使用它進行發行管理"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 4e1de756696bec4d3d0ff443e643d735523b851b


---
# <a name="create-a-logic-app-deployment-template"></a>建立邏輯應用程式部署範本
建立邏輯應用程式之後，您可能想要將其建立為 Azure Resource Manager 範本。 如此一來，您就可以輕鬆地將邏輯應用程式部署到您可能需要在其中用到它的任何環境或資源群組。 如需 Resource Manager 範本的簡介，請務必查看[編寫 Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)和[使用 Azure Resource Manager 範本部署資源](../azure-resource-manager/resource-group-template-deploy.md)文章。

## <a name="logic-app-deployment-template"></a>邏輯應用程式部署範本
邏輯應用程式有三個基本元件：

* **邏輯應用程式資源**。 這個資源包含定價方案、位置和工作流程定義等相關資訊。
* **工作流程定義**。 這就是您在程式碼檢視中看到的內容。 它包含流程步驟的定義，以及引擎應如何執行。 這是邏輯應用程式資源的 `definition` 屬性。
* **連接**。 有一些個別資源可安全地儲存關於任何連接器連接的中繼資料，例如連接字串和存取權杖。 您可以在邏輯應用程式中，於邏輯應用程式資源的 `parameters` 區段中參考這些連接。

您可以使用 [Azure 資源總管](http://resources.azure.com)等工具，檢視現有邏輯應用程式的這些所有部分。

若要讓邏輯應用程式的範本可與資源群組部署搭配使用，您需要定義資源並視需要參數化。 例如，如果部署到開發、測試和生產環境，您可能想要在每個環境中使用不同連接字串連至 SQL Database， 或者，您可能想要在不同訂用帳戶或資源群組內部署。  

## <a name="create-a-logic-app-deployment-template"></a>建立邏輯應用程式部署範本
具備有效的邏輯應用程式部署範本的最簡單方法是使用[適用於 Logic Apps 的 Visual Studio 工具](logic-apps-deploy-from-vs.md)。  Visual Studio 工具產生的有效部署範本，可以在任何訂用帳戶或位置。

有一些其他工具可在您建立邏輯應用程式部署範本時提供協助。 您可以手動撰寫，也就是視需要使用此處已討論過的資源來建立參數。 另一個選項是使用 [邏輯應用程式範本建立者](https://github.com/jeffhollan/LogicAppTemplateCreator) PowerShell 模組。 這個開放原始碼模組會先評估邏輯應用程式和它所使用的任何連接，然後以部署所需的參數產生範本資源。 例如，如果您的邏輯應用程式收到來自 Azure 服務匯流排佇列的訊息並將資料加入至 Azure SQL Database，則此工具將會保留所有的協調流程邏輯並將 SQL 和服務匯流排連接字串參數化，如此便可在部署時進行設定。

> [!NOTE]
> 連接必須位於與邏輯應用程式相同的資源群組內。
>
>

### <a name="install-the-logic-app-template-powershell-module"></a>安裝邏輯應用程式範本 PowerShell 模組
最簡單的安裝方式是透過 [PowerShell 資源庫](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1)使用 `Install-Module -Name LogicAppTemplate` 命令。  

您也可以手動安裝 PowerShell 模組：

1. 下載最新版的 [邏輯應用程式範本建立者](https://github.com/jeffhollan/LogicAppTemplateCreator/releases)。  
2. 將此資料夾解壓縮至您的 PowerShell 模組資料夾 (通常是 `%UserProfile%\Documents\WindowsPowerShell\Modules`)。

為了讓模組能使用任何租用戶和訂用帳戶存取權杖，我們建議您搭配 [ARMClient](https://github.com/projectkudu/ARMClient) 命令列工具使用。  這篇 [部落格文章 ](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) 會更詳細地討論 ARMClient。

### <a name="generate-a-logic-app-template-by-using-powershell"></a>使用 PowerShell 產生邏輯應用程式範本
安裝 PowerShell 之後，您可以使用下列命令來產生範本：

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` 是 Azure 訂用帳戶識別碼。 這行程式碼會先透過 ARMClient 取得存取權杖，然後透過管線將它傳送到 PowerShell 指令碼，接著在 JSON 檔案中建立範本。

## <a name="add-parameters-to-a-logic-app-template"></a>將參數加入至邏輯應用程式範本
建立邏輯應用程式範本之後，您可以繼續加入或修改您可能需要的參數。 例如，如果您的定義包含您打算在單一部署中部署的 Azure 函數或巢狀工作流程的資源識別碼，您可以將更多資源新增至範本，並視需要將識別碼參數化。 相同情況亦適用於您預計要與各資源群組一起部署的自訂 API 或 Swagger 端點的任何參考。

## <a name="deploy-a-logic-app-template"></a>部署邏輯應用程式範本
您可以使用多項工具部署範本，包括 PowerShell、REST API、Visual Studio Release Management 及 Azure 入口網站範本部署。 如需詳細資訊，請參閱這篇關於 [使用 Azure Resource Manager 範本部署資源](../azure-resource-manager/resource-group-template-deploy.md) 的文章。 此外，我們也建議您建立 [參數檔案](../azure-resource-manager/resource-group-template-deploy.md#parameters) 來儲存參數值。

### <a name="authorize-oauth-connections"></a>授權 OAuth 連接
部署之後，邏輯應用程式就能搭配有效參數端對端運作。 不過，OAuth 連接仍然必須經過授權，才能產生有效的存取權杖。 您可以在設計工具中開啟邏輯應用程式，然後授權連接，藉以執行此動作。 或者，如果您想要自動化，您可以使用指令碼來同意每個 OAuth 連接。 在 GitHub 上的 [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) 專案下方有一個範例指令碼。

## <a name="visual-studio-release-management"></a>Visual Studio Release Management
適用於部署和管理環境的常見案例是搭配使用 Visual Studio Release Management 之類的工具與邏輯應用程式部署範本。 Visual Studio Team Services 包含可加入至任何組建或版本管線的 [部署 Azure 資源群組](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) 工作。 您必須擁有 [服務主體](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) 才能授權部署，而後可以產生版本定義。

1. 在 Release Management 中，若要建立新的定義，請選取 [空白]，使用空白定義來開始。

    ![建立新的空白定義][1]   
2. 選擇任何您需要的資源。 這很可能是以手動方式產生或在建置過程中產生的邏輯應用程式範本。
3. 新增 [Azure 資源群組部署]  工作。
4. 設定 [服務主體](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)並參考 [範本] 和 [範本參數] 檔案。
5. 視需要針對任何其他環境、自動化測試或核准者，繼續在發行程序中建置步驟。

<!-- Image References -->
[1]: ./media/logic-apps-create-deploy-template/emptyreleasedefinition.png



<!--HONumber=Jan17_HO3-->


