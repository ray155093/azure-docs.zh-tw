<properties
   pageTitle="Azure Functions 概觀 | Microsoft Azure"
   description="建立可在數分鐘內撰寫的簡單函式，以了解 Azure Functions 如何最佳化非同步工作負載。"
   services="functions"
   documentationCenter="na"
   authors="mattchenderson"
   manager="erikre"
   editor=""
   tags=""
   keywords="azure functions, 函式, 事件處理, webhook, 動態計算, 無伺服器架構"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="04/21/2016"
   ms.author="cfowler;mahender"/>
   
   
# Azure Functions 概觀

本主題提供 Azure Functions 的高階概觀。如果您想要直接進入正題並開始使用 Azure Functions，請從[建立您的第一個 Azure 函式](functions-create-first-azure-function.md)著手。如果您要尋找更多有關 Functions 的技術資訊，請參閱[開發人員參考](functions-reference.md)。

## 更快撰寫函式的方法

在一分鐘內撰寫任何函式 - 當您需要簡單作業來清理資料庫，或建置可處理來自所連接裝置的數百萬則訊息的功能時。使用您選擇的開發語言 (C#、Node.JS、Python 等)。只需對您的程式碼執行的時間付費，並信任 Azure 視需要調整。

Azure Functions 是可在雲端輕鬆執行程式碼片段或「函式」的解決方案。您可以只撰寫處理手邊問題所需的程式碼，而不需擔心要執行它的整個應用程式或基礎結構。這可讓開發更具生產力，而且您可以在幾分鐘內[開始使用您的第一個函式](functions-create-first-azure-function.md)。

## 我可以用 Functions 來做什麼？

Azure Functions 是處理資料、整合系統、使用物聯網 (IoT)，以及建置簡單 API 和微服務的絕佳解決方案。

Functions 包含常見案例的範本資源庫，包括︰

* 回應 GitHub Webhook 要求
* 調整已上傳至 Azure 儲存體的影像大小
* 使用訂單處理佇列
* 還有更多功能待您探索！ 

若要深入了解 Functions 的運作方式及某些範例組態，請參閱[開發人員參考](functions-reference.md)。

## 特性

Azure Functions 是符合企業需求的全功能平台，附有可讓複雜的整合和連線工作變簡單的功能。透過這一組核心功能，使用 Azure Functions 的開發人員只要專注於其目標，不用將基礎結構項目放在一起，進而可能變得更具生產力。

Azure Functions 包含下列功能︰
    
* **選擇的語言** - 使用 C#、Node.js、Python、F#、PHP、batch、bash、Java 或任何可執行檔撰寫函式。  
* **使用即付費價格模式** - 只對執行您的程式碼所花的時間付費。請參閱底下[價格](#pricing)一節中的「動態 App Service 方案」選項。  
* **自備相依性** - Functions 支援 NuGet 和 NPM，以便您使用您最愛的程式庫。  
* **整合式安全性** - 利用 OAuth 提供者 (如 Azure Active Directory、Facebook、Google、Twitter 和 Microsoft 帳戶) 保護 HTTP 觸發的函式。  
* **無程式碼的整合** - 輕鬆地利用 Azure 服務和軟體即服務 (SaaS) 供應項目。請參閱底下[整合](#integrations)一節中的相關範例。  
* **彈性開發** - 以入口網站內的編輯器修改您的函式，或透過 GitHub、Visual Studio Team Services 等設定連續整合和部署程式碼。  
* **開放原始碼** - Functions 是開放原始碼的平台並[可在 GitHub 上取得](https://github.com/azure/azure-webjobs-sdk-script)。  

### <a name="integrations"></a>整合

Azure Functions 支援各種與 Azure 和第三方服務的整合。您可以利用這些整合來觸發您的函式並開始執行，或做為您的程式碼的輸入和輸出。下表顯示 Azure Functions 所支援的一些範例整合。

[AZURE.INCLUDE [動態計算](../../includes/functions-bindings.md)]

## <a name="pricing"></a>Functions 的計費方式

執行 Azure Functions 的方式有兩種︰使用動態 App Service 方案及使用傳統 App Service 方案。

在**動態 App Service 方案**中，您不必擔心資源管理。每次執行您的函式時，Azure 會提供所有必要的運算資源。您只需對您的程式碼的執行時間付費。在 [Functions 價格](/pricing/details/functions)頁面上可取得完整的價格詳細資料。

**傳統 App Service 方案**可讓您如同 Web、行動及 API 應用程式一樣執行函式。如果您已準備對其他應用程式使用 App Service，這是絕佳的解決方案 - 您的函式可以在相同方案上執行，不會產生額外的費用。如需完整的詳細資訊，請參閱 [App Service 價格](/pricing/details/app-service/)頁面。

## 報告問題

[AZURE.INCLUDE [報告問題](../../includes/functions-reporting-issues.md)]

##後續步驟

+ [建立您的第一個 Azure 函式](functions-create-first-azure-function.md) 直接進入正題並使用 Azure Functions 快速入門建立您的第一個函式。 
+ [Azure Functions 開發人員參考](functions-reference.md) 提供更多有關 Azure Functions 執行階段的技術資訊，以及可供撰寫函式程式碼及定義觸發程序和繫結時參考。
+ [測試 Azure Functions](functions-test-a-function.md) 說明可用於測試函式的各種工具和技巧。
+ [如何調整 Azure 函數](functions-scale.md) 討論 Azure Functions 可用的服務方案，包括動態服務方案，以及如何選擇正確的方案。 
+ [什麼是 Azure 應用程式服務？](../app-service/app-service-value-prop-what-is.md) Azure Functions 會利用 Azure App Service 平台執行核心功能，例如部署、環境變數和診斷。 

<!---HONumber=AcomDC_0427_2016-->