<properties
	pageTitle="Azure Functions Mobile Apps 繫結 | Microsoft Azure"
	description="了解如何在 Azure Functions 中使用 Azure Mobile Apps 繫結。"
	services="functions"
	documentationCenter="na"
	authors="ggailey777"
	manager="erikre"
	editor=""
	tags=""
	keywords="azure functions, 函數, 事件處理, 動態運算, 無伺服器架構"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/30/2016"
	ms.author="glenga"/>

# Azure Functions Mobile Apps 繫結

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

這篇文章說明如何在 Azure Functions 中為 Azure Mobile Apps 繫結進行設定及撰寫程式碼。

[AZURE.INCLUDE [簡介](../../includes/functions-bindings-intro.md)]

Azure App Service Mobile Apps 可讓您將資料表端點資料公開至行動用戶端。此相同的表格式資料可用於 Azure Functions 的輸入和輸出繫結中。因為它支援動態結構描述，因此 Node.js 後端行動應用程式非常適合用來公開表格式資料以用於您的函式。動態結構描述預設會啟用，而且應該在生產行動應用程式中停用。如需有關 Node.js 後端中資料表端點的詳細資訊，請參閱[概觀：資料表作業](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations)。在 Mobile Apps 中，Node.js 後端支援在入口網站內瀏覽和編輯資料表。如需詳細資訊，請參閱 Node.js SDK 主題中的[入口網站內編輯](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#in-portal-editing)。當您搭配使用 .NET 後端行動應用程式與 Azure Functions 時，您必須將資料模型手動更新為函式所要求的形式。如需有關 .NET 後端行動應用程式中資料表端點的詳細資訊，請參閱 .NET 後端 SDK 主題中的[做法：定義資料表控制器](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller)。

## 針對您的行動應用程式後端 URL 建立環境變數

Mobile Apps 繫結目前會要求您建立環境變數，以傳回行動應用程式後端本身的 URL。您可以藉由尋找您的行動應用程式並開啟刀鋒視窗，在 [Azure 入口網站](https://portal.azure.com)中找到這個 URL。

![Azure 入口網站中的 Mobile Apps 刀鋒視窗](./media/functions-bindings-mobile-apps/mobile-app-blade.png)

在您的函數應用程式中設定此 URL 做為環境變數：

1. 在 [Azure Functions 入口網站](https://functions.azure.com/signin)的函式應用程式中，按一下 [函式應用程式設定] > [移至 App Service 設定]。

	![函數應用程式設定刀鋒視窗](./media/functions-bindings-mobile-apps/functions-app-service-settings.png)

2. 在函式應用程式中，按一下 [所有設定]、向下捲動至 [應用程式設定]，然後在 [應用程式設定] 下方輸入環境變數的新**名稱**、將 URL 貼至 [值]、確定使用 HTTPS 配置，然後按一下 [儲存] 並關閉函式應用程式刀鋒視窗，以返回 Functions 入口網站。

	![新增應用程式設定環境變數](./media/functions-bindings-mobile-apps/functions-app-add-app-setting.png)

您現在可以設定這個新的環境變數，做為您繫結中的「連接」欄位。

## <a id="mobiletablesapikey"></a>使用 API 金鑰來保護對 Mobile Apps 資料表端點的存取。

在 Azure Functions 中，行動資料表繫結可讓您指定 API 金鑰，也就是可用來防止您的函式以外的應用程式存取共用的密碼。Mobile Apps 並沒有內建支援的 API 金鑰驗證。不過，您可以依照[實作 API 金鑰的 Azure App Service Mobile Apps 後端](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)中的範例，在 Node.js 後端行動應用程式中實作 API 金鑰。您也可以用同樣的方式，在 [.NET 後端行動應用程式](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key)中實作 API 金鑰。

>[AZURE.IMPORTANT] 此 API 金鑰不可與您的行動應用程式用戶端一起散佈，它應該只安全地散佈給服務端用戶端，如 Azure Functions。

## <a id="mobiletablesinput"></a>Azure Mobile Apps 輸入繫結

輸入繫結可從行動資料表端點載入記錄，並將它直接傳遞至繫結。您可以根據叫用該函式的觸發程序來判斷記錄識別碼。在 C# 函式中，當函式成功結束時，會將記錄所做的任何變更自動傳回資料表。

#### Mobile Apps 輸入繫結的 function.json

「function.json」檔案支援下列屬性：

- `name`︰函式程式碼中用於新記錄的變數名稱。
- `type`︰繫結類型必須設為「mobileTable」。
- `tableName`︰其中將建立新記錄的資料表。
- `id`：要擷取之記錄的識別碼。此屬性支援類似於 `{queueTrigger}` 的繫結，此繫結會使用佇列訊息的字串值做為記錄識別碼。
- `apiKey`︰字串，此字串是為行動應用程式指定選擇性 API 金鑰的應用程式設定。當您的行動應用程式使用 API 金鑰來限制用戶端存取時，這是必要的選項。
- `connection`︰應用程式設定中環境變數名稱的字串，可指定行動應用程式後端的 URL。
- `direction`︰繫結方向，必須設定為「in」。

範例「function.json」檔案：

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "mobileTable",
	      "tableName": "MyTable",
	      "id" : "{queueTrigger}",
	      "connection": "My_MobileApp_Url",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### C# 佇列觸發程序的 Azure Mobile Apps 程式碼範例

根據上述範例 function.json，輸入繫結會從 Mobile Apps 資料表端點擷取識別碼符合佇列訊息字串的記錄，並將它傳遞給「record」參數。找不到記錄時，參數為 null。接著，當函式結束時，會以新的「Text」值更新記錄。

	#r "Newtonsoft.Json"	
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, JObject record)
	{
	    if (record != null)
	    {
	        record["Text"] = "This has changed.";
	    }    
	}

#### Node.js 佇列觸發程序的 Azure Mobile Apps 程式碼範例

根據上述範例 function.json，輸入繫結會從 Mobile Apps 資料表端點擷取識別碼符合佇列訊息字串的記錄，並將它傳遞給「record」參數。在 Node.js 函式中，更新的記錄不會傳回至資料表。這個程式碼範例會將擷取的記錄寫入記錄檔。

	module.exports = function (context, input) {    
	    context.log(context.bindings.record);
	    context.done();
	};


## <a id="mobiletablesoutput"></a>Azure Mobile Apps 輸出繫結

您的函式可以使用輸出繫結，將記錄寫入 Mobile Apps 資料表端點。

#### Mobile Apps 輸出繫結的 function.json

function.json 檔案支援下列屬性：

- `name`︰函式程式碼中用於新記錄的變數名稱。
- `type` ︰必須設為「mobileTable」的繫結類型。
- `tableName`︰其中將建立新記錄的資料表。
- `apiKey`︰字串，此字串是為行動應用程式指定選擇性 API 金鑰的應用程式設定。當您的行動應用程式使用 API 金鑰來限制用戶端存取時，這是必要的選項。
- `connection`︰應用程式設定中環境變數名稱的字串，可指定行動應用程式後端的 URL。
- `direction`︰繫結方向，必須設定為「out」。

function.json 範例：

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "mobileTable",
	      "tableName": "MyTable",
	      "connection": "My_MobileApp_Url",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

#### C# 佇列觸發程序的 Azure Mobile Apps 程式碼範例

這個 C# 程式碼範例會將新記錄插入 Mobile Apps 資料表端點，並將「Text」屬性插入上述繫結中指定的資料表。

	public static void Run(string myQueueItem, out object record)
	{
	    record = new {
	        Text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}

#### Node.js 佇列觸發程序的 Azure Mobile Apps 程式碼範例

這個 Node.js 程式碼範例會將新記錄插入 Mobile Apps 資料表端點，並將「text」屬性插入上述繫結中指定的資料表。

	module.exports = function (context, input) {
	
	    context.bindings.record = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	
	    context.done();
	};

## 後續步驟

[AZURE.INCLUDE [後續步驟](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0907_2016-->