<properties
   pageTitle="建立 Logic Apps 部署範本 | Microsoft Azure"
   description="了解如何建立 Logic Apps 部署範本及如何用於版本管理。"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/25/2016"
   ms.author="jehollan"/>
   
# 建立 Logic Apps 部署範本

一旦建立邏輯應用程式，您可能想要將它建立為 Azure 資源範本，以便輕鬆地將邏輯應用程式部署到您所需的任何環境或資源群組。如需 Resource Manager 範本的簡介，請務必查看[編寫 Azure Resource Manager 範本](../resource-group-authoring-templates.md)和[使用 Azure Resource Manager 範本部署資源](../resource-group-template-deploy.md)文章

## 邏輯應用程式部署範本概觀

邏輯應用程式是由 3 個基本元件所組成︰

* **邏輯應用程式資源** - 包含價格計畫、位置和工作流程定義等相關資訊的資源。
* **工作流程定義** - 當您選取 [程式碼檢視] 時所看見的內容 - 流程的步驟以及如何執行引擎的定義。這是邏輯應用程式資源的 `definition` 屬性。
* **連線** - 區隔資源以便安全地儲存任何連接器連線相關中繼資料，例如連接字串和存取權杖。在邏輯應用程式資源的 `parameters` 中會參考這些連線。

您可以使用 [Azure 資源總管](http://resources.azure.com)等工具，檢視現有 Logic Apps 的這些部分。

若要讓邏輯應用程式的範本可用於資源群組部署，您需要定義資源並視需要參數化。例如，如果部署到開發、測試和生產環境，您可能想要在每個環境中使用 SQL Database 的不同連接字串，或在不同的訂用帳戶或資源群組中部署。

## 建立邏輯應用程式部署範本

有幾個工具可協助您建立邏輯應用程式部署範本。您可以採用上述資源並且視需要建立參數，以手動方式編寫。您也可以使用[邏輯應用程式範本建立者](https://github.com/jeffhollan/LogicAppTemplateCreator) PowerShell 模組。開放原始碼模組的運作方式是評估邏輯應用程式和它所用的任何連線，並以部署所需的參數產生範本資源。比方說，如果您的邏輯應用程式收到來自服務匯流排佇列的訊息並將資料加入至 SQL Azure 資料庫，則此工具會保留所有的協調流程邏輯並將 SQL 和服務匯流排連接字串參數化，以便在部署時進行設定。

>[AZURE.NOTE] 連線必須位於與邏輯應用程式相同的資源群組內

### 安裝邏輯應用程式範本 PowerShell 模組

最簡單的安裝方式是透過 [PowerShell 資源庫](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1) 搭配 `Install-Module -Name LogicAppTemplate` 命令。

您也可以手動安裝︰

1. 下載最新版的[邏輯應用程式範本建立者](https://github.com/jeffhollan/LogicAppTemplateCreator/releases)。  
1. 將此資料夾解壓縮至您的 PowerShell 模組資料夾 (通常是 `%UserProfile%\Documents\WindowsPowerShell\Modules`)

為了讓模組能使用任何租用戶和訂用帳戶存取權杖，建議您搭配 [armclient](https://github.com/projectkudu/ARMClient) 命令列工具使用。在[這裡](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)可以找到 `armclient` 的詳細資料。

### 透過 PowerShell 產生邏輯應用程式範本

安裝後，您現在可以使用下列命令產生範本︰

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

其中 `$SubscriptionId` 是 Azure 訂用帳戶識別碼。此程式碼行會先透過 `armclient` 取得存取權杖、透過管線將它傳送至 PowerShell 指令碼，然後將範本輸出至 `.json` 檔案。

## 將參數新增至邏輯應用程式範本

擁有邏輯應用程式範本後，您可以繼續新增或修改任何可能需要的其他參數。比方說，如果您的定義包含您打算在單一部署中部署之 Azure 函式或巢狀工作流程的資源識別碼，您會想將其他資源新增至範本和並視需要將識別碼參數化。相同情況亦適用於您預計要與各資源群組一起部署的自訂 API 或 Swagger 端點的任何參考。

## 部署邏輯應用程式範本

擁有範本後，您可以使用多項工具進行部署，包括 PowerShell、REST API、Visual Studio、Release Management 或 Azure 入口網站範本部署。在[這裡](../resource-group-template-deploy.md)可以找到部署詳細資料。建議您也建立[參數檔案](../resource-group-template-deploy.md#parameter-file)來儲存參數值。

### 授權 OAuth 連線

部署之後，邏輯應用程式會以端對端方式使用有效的參數，但 OAuth 連線仍需要經過授權，才能產生有效的存取權杖。您可以在設計工具中開啟邏輯應用程式並授權連線來達成目的，而如果您想要自動化，也可以使用指令碼來同意每個 OAuth 連線。在 GitHub 的[連線授權](https://github.com/logicappsio/LogicAppConnectionAuth)專案之下可以找到範例指令碼。

## Visual Studio 版本管理

部署和管理環境的常見案例是搭配使用 Visual Studio Release Management 之類的工具與邏輯應用程式部署範本。VSTS 包含可加入至任何組建或版本管線的[部署 Azure 資源群組](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup)工作。您必須擁有[服務主體](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)才能授權部署，而後可以產生版本定義。

1. 在 [版本] 之下選取開始具有 [空白] 定義的 [新定義]。

    ![][1]

1. 選擇任何您需要的成品。這可能是以手動方式產生或在建置過程中產生的邏輯應用程式範本。
1. 新增 [Azure 資源群組部署] 工作。
1. 設定[服務主體](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)並參考 [範本] 和 [範本參數] 檔案。
1. 繼續針對任何其他環境、自動化測試或核准者 (視需要)，在發行程序中建置步驟。
    
<!-- Image References -->
[1]: ./media/app-service-logic-create-deploy-template/emptyReleaseDefinition.PNG

<!---HONumber=AcomDC_0601_2016-->