<properties
   pageTitle="從 Azure 入口網站建立函式 | Microsoft Azure"
   description="在兩分鐘內建立您的第一個 Azure 函式 (無伺服器的應用程式)。"
   services="functions"
   documentationCenter="na"
   authors="ggailey777"
   manager="erikre"
   editor=""
   tags=""
/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="09/08/2016"
   ms.author="glenga"/>

#從 Azure 入口網站建立函式

##概觀
Azure Functions 是事件取向的隨選計算體驗並擴充了現有的 Azure 應用程式平台，使其可實作其他 Azure 服務、SaaS 產品和內部部署系統內事件所觸發的程式碼。透過 Azure Functions，您的應用程式會根據需求調整，而您只需要支付所用資源的費用。Azure Functions 可您建立以各種程式設計語言實作的已排程或已觸發程式碼單位。若要深入了解 Azure Functions，請參閱 [Azure Functions 概觀](functions-overview.md)。

本主題說明如何使用 Azure 入口網站建立由 HTTP 觸發程序叫用的簡單 "hello world" Node.js Azure 函式。在您可以於 Azure 入口網站中建立函式之前，您必須在 Azure App Service 中明確建立一個函數應用程式。如果要讓系統為您自動建立函數應用程式，請參閱[另一篇 Azure Functions 快速入門教學課程](functions-create-first-azure-function.md)，該文章提供較為簡單的快速入門體驗，且包含一部影片。

##建立函數應用程式

函式應用程式可在 Azure 中主控函式的執行。請遵循下列步驟來在 Azure 入口網站中建立函數應用程式。

您必須先具備有效的 Azure 帳戶，才可以建立第一個函式。如果您還沒有 Azure 帳戶，[可以使用免費帳戶](https://azure.microsoft.com/free/)。

1. 移至 [Azure 入口網站](https://portal.azure.com)，然後以您的 Azure 帳戶登入。

2. 按一下 [+新增] > [Web + 行動] > [函數應用程式]，選取您的 [訂用帳戶]，輸入可識別您函數應用程式的唯一 [應用程式名稱]，然後指定下列設定：

	+ **[資源群組](../azure-portal/resource-group-portal.md/)**：選取 [新建] 並為您的新資源群組輸入名稱。您也可以選擇現有的資源群組，不過您可能無法為函數應用程式建立動態的 App Service 方案。
	+ **[App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)**：選擇 [動態] 或 [傳統]。
		+ **動態**：Azure Functions 的預設方案類型。當您選擇動態方案時，您也必須選擇 [位置] 並設定 [記憶體配置] \(以 MB 為單位)。如需記憶體配置如何影響成本的詳細資訊，請參閱 [Azure Functions 定價](https://azure.microsoft.com/pricing/details/functions/)。
		+ **傳統**：傳統 App Service 方案需要您建立一個 (或選取現有的)「App Service 方案/位置」。這些設定決定與您的應用程式相關聯的[位置、功能、成本和計算資源](https://azure.microsoft.com/pricing/details/app-service/)。
	+ **儲存體帳戶**：每個函數應用程式都需要一個儲存體帳戶。您可以選擇現有的儲存體帳戶或是建立帳戶。

	![在 Azure 入口網站中建立新的函數應用程式](./media/functions-create-first-azure-function-azure-portal/function-app-create-flow.png)

3. 按一下 [建立] 以佈建並部署新的函數應用程式。

佈建函數應用程式之後，您便可以建立您的第一個函式。

## 建立函式

這些步驟會從 Azure Functions 快速入門建立一個函式。

1. 在 [快速入門] 索引標籤中，按一下 [WebHook + API] 及 [JavaScript]，然後按一下 [建立函式]。隨即建立新的預先定義的 Node.js 函式。

	![](./media/functions-create-first-azure-function-azure-portal/function-app-quickstart-node-webhook.png)

2. (選擇性) 此時，您可以在快速入門中，選擇在入口網站中進行 Azure Functions 功能的快速導覽。一旦完成或跳過本教學課程，您可以使用 HTTP 觸發程序來測試新函式。

##測試函式

由於 Azure Functions 快速入門包含功能程式碼，您可以立即測試您的新函式。

1. 在 [開發] 索引標籤中，檢閱 [程式碼] 視窗，並請注意此 Node.js 程式碼預期 HTTP 要求有在訊息內文中或在查詢字串中傳遞的 *name* 值。此函式執行時，回應訊息中會傳回這個值。

	![](./media/functions-create-first-azure-function-azure-portal/function-app-develop-tab-testing.png)

2. 向下捲動至 [要求本文] 文字方塊，將 name 屬性的值變更為您的名稱，然後按一下 [執行]。您會看到執行是由測試 HTTP 要求所觸發，資訊會寫入至串流記錄檔，而 "hello" 回應會顯示在 [輸出] 中。

3. 若要從另一個瀏覽器視窗或索引標籤觸發相同函式的執行，請從 [開發] 索引標籤複製 [函式 URL] 值並貼到瀏覽器網址列中，然後附加查詢字串值 `&name=yourname` 並按 Enter 鍵。相同的資訊會寫入至記錄檔，而瀏覽器會像之前一樣顯示 "hello" 回應。

##後續步驟

本快速入門示範非常簡單的基本 HTTP 觸發函式執行。如需有關在您的應用程式中使用 Azure Functions 功能的詳細資訊，請參閱下列主題。

+ [Azure Functions 開發人員參考](functions-reference.md) 可供程式設計人員撰寫函式程式碼及定義觸發程序和繫結時參考。
+ [測試 Azure Functions](functions-test-a-function.md) 說明可用於測試函式的各種工具和技巧。
+ [如何調整 Azure 函數](functions-scale.md) 討論 Azure Functions 可用的服務方案，包括動態服務方案，以及如何選擇正確的方案。
+ [什麼是 Azure App Service？](../app-service/app-service-value-prop-what-is.md) Azure Functions 會使用 Azure App Service 平台執行核心功能，例如部署、環境變數和診斷。

[AZURE.INCLUDE [開始使用注意事項](../../includes/functions-get-help.md)]

<!---HONumber=AcomDC_0914_2016-->