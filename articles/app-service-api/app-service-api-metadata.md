<properties
	pageTitle="適用於 API 探索及產生程式碼用的 App Service API Apps 中繼資料 | Microsoft Azure"
	description="了解 Azure App Service 中的 API 應用程式如何使用 Swagger 中繼資料來協助 API 探索和產生程式碼。"
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/02/2016"
	ms.author="tdykstra"/>

# 適用於 API 探索及產生程式碼用的 App Service API Apps 中繼資料 

App Service API Apps 內建支援 [Swagger 2.0](http://swagger.io/) API 中繼資料。您不需要使用 Swagger，但如果您使用 Swagger，就能利用可讓探索和使用變得更加容易的 API Apps 功能。

## Swagger 端點

您可以指定端點來為 API 應用程式提供 Swagger 2.0 JSON 中繼資料，做為該 API 應用程式的屬性。端點可以與 API 應用程式的基底 URL 或絕對 URL 相關。絕對 URL 可以指向 API 應用程式以外的地方。

許多下游的用戶端 (例如，Visual Studio 程式碼產生和 PowerApps 的「新增 API」流程)，此 URL 必須可公開存取 (未受使用者或服務驗證所保護)。這表示如果您是使用 App Service 驗證，而且想要從應用程式本身之中公開 API 定義，您就需要使用驗證選項，來允許匿名流量進入您的 API。如需詳細資訊，請參閱 [App Service API Apps 的驗證和授權](app-service-api-authentication.md)。

### 入口網站刀鋒視窗

在 [Azure 入口網站](https://portal.azure.com/)中，您可以在 [API 定義] 刀鋒視窗看到及變更端點 URL 。

![](./media/app-service-api-metadata/apidefblade.png)

### Azure Resource Manager 屬性

您也可以使用 [Azure PowerShell](../powershell-install-configure.md) 和 [Azure CLI](../xplat-cli-install.md) 等命令列工具中的[資源總管](https://resources.azure.com/)或 [Azure Resource Manager 範本](../resource-group-authoring-templates.md)，設定 API 應用程式的 API 定義。

例如，在**資源總管**中移至 **訂用帳戶 > {您的訂用帳戶} > resourceGroups > {您的資源群組} > 提供者 > [Microsoft.Web] > 網站 > {您的網站} > 組態 > web**，您就會看到 `apiDefinition` 屬性：

		"apiDefinition": {
		  "url": "https://contactslistapi.azurewebsites.net/swagger/docs/v1"
		}

如需可設定 `apiDefinition` 屬性之 Azure Resource Manager 範本的範例，請開啟[待辦事項清單範例應用程式中的 azuredeploy.json 檔案](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json)。尋找看起來如以上 JSON 範例的範本區段。

### 預設值

當您使用 Visual Studio 建立 API 應用程式時，API 定義端點會自動設為 API 應用程式的基底 URL，加上 `/swagger/docs/v1`。這是 [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) NuGet 封裝用來為 ASP.NET Web API 專案提供動態產生的 Swagger 中繼資料的預設 URL。

## 產生程式碼

將 Swagger 整合到 Azure API 應用程式的好處之一，就是自動產生程式碼。產生的用戶端類別讓您能更容易地撰寫會呼叫 API 應用程式的程式碼。

您可以利用 Visual Studio，或從命令列來為 API 應用程式產生用戶端程式碼。如需如何在 Visual Studio 中為 ASP.NET Web API 專案產生用戶端類別的相關資訊，請參閱[開始使用 API Apps 和 ASP.NET](app-service-api-dotnet-get-started.md#codegen)。如需如何為所有支援的語言從命令列進行這項作業的相關資訊，請參閱 GitHub.com 上 [Azure/autorest](https://github.com/azure/autorest) 儲存機制的讀我檔案。
 
## 後續步驟

如需逐步解說的教學課程來引導您建立、部署及使用 API 應用程式，請參閱[開始使用 Azure App Service 中的 API Apps](app-service-api-dotnet-get-started.md)。

如果您搭配使用 Azure API 管理與 API Apps，您可以使用 Swagger 元資料將 API 匯入 API 管理。如需詳細資訊，請參閱[如何在 Azure API 管理中連同操作一起匯入 API 的定義](../api-management/api-management-howto-import-api.md)。

<!---HONumber=AcomDC_0601_2016-->