---
title: "Azure Functions 中的 OpenAPI 中繼資料 | Microsoft Docs"
description: "Azure Functions 中的 OpenAPI 支援概觀"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/23/2017
ms.author: alkarche
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 08063960d3956ca65dd32d9fe726c366799a2087
ms.lasthandoff: 04/18/2017


---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Azure Functions 中的 OpenAPI 2.0 中繼資料支援 (預覽)
此預覽功能可讓您在函數應用程式中撰寫 OpenAPI 2.0 (先前稱為 Swagger) 定義，並使用函數應用程式裝載該檔案。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

### <a name="what-is-openapi-metadata"></a>OpenAPI 中繼資料是什麼？
[OpenAPI 中繼資料](http://swagger.io/)可讓裝載 REST API 的函數供其他各種軟體使用。 包括第一方提供的 PowerApps 和 [API Apps](https://docs.microsoft.com/azure/app-service-api/app-service-api-dotnet-get-started#a-idcodegena-generate-client-code-for-the-data-tier) 等，和 [Postman](https://www.getpostman.com/docs/importing_swagger) 等第三方開發人員工具，以及[更多套件](http://swagger.io/tools/)。

>[!TIP]
>建議您從[入門教學課程](./functions-api-definition-getting-started.md)開始，然後再回到此文件學習更多特定的功能。

## <a name="enable"></a>啟用 OpenAPI 定義支援
* 所有 OpenAPI 設定都能在您函數應用程式設定的 [`API Definition (preview)`] 頁面中設定。
* 將 `API defintion source` 設定為 [`Function`]，以啟用裝載的 OpenAPI 定義，並立即開始產生定義。
  * `External URL` 可讓您的函數使用裝載在其他位置的 OpenAPI 定義。

## <a name="generate-defintion"></a>從您的函數中繼資料產生 Swagger 基本架構
使用範本式開始撰寫您的第一個 OpenAPI 定義是一種絕佳方式。 定義範本功能會針對每個 HTTP 觸發程序函數，使用 function.json 中所有的中繼資料來建立鬆散的 OpenAPI 定義。 **您會需要從 [OpenAPI 規格 (英文)](http://swagger.io/specification/) 填入 API 的更多相關資訊，例如要求和回應範本。**

[查看此入門教學課程以取得逐步指示](./functions-api-definition-getting-started.md)

### <a name="templates"></a>可用範本

|名稱| 描述 |
|:-----|:-----|
|已產生的定義|具有可從函數現有中繼資料推斷的最大數量資訊的 OpenAPI定義|

### <a name="quickstart-details"></a>產生的定義中包含的中繼資料

下表顯示入口網站設定和 function.json 中相對應的資料，如同對應到所產生的 Swagger 基本架構。

|Swagger.json|入口網站 UI|Function.json|
|:----|:-----|:-----|
|[Host](http://swagger.io/specification/#fixed-fields-15)|`Function app settings` > `Go to App Service Settings` > `Overview` > `URL`|*不存在*
|[Paths](http://swagger.io/specification/#paths-object-29)|`Integrate` > `Selected HTTP methods`|繫結：路由
|[Path Item](http://swagger.io/specification/#path-item-object-32)|`Integrate` > `Route template`|繫結：方法
|[Security](http://swagger.io/specification/#security-scheme-object-112)|金鑰|*不存在*|
|operationID*|路由 + 允許的動詞|路由 + 允許的動詞|

\*只有與 PowerApps + Flow 整合才需要 Operation ID
> [!NOTE]
>  x-ms-summary 提供 Logic Apps、Flow 和 PowerApps 中的顯示名稱。
>
> 如需詳細資訊，請參閱[為 PowerApps 自訂 Swagger 定義](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/)。

## <a name="CICD"></a>使用 CI/CD 設定 API 定義

 在從原始檔控制啟用讓原始檔控制修改 API 定義之前，您必須先啟用裝載在入口網站中的 API 定義。 請遵循下列指示：

1. 在您的函數應用程式設定中，瀏覽到 `API Definition (preview)` (API 定義 (預覽))。
  1. 將 `API definition source` (API 定義來源) 設定為 [`Function`]
  1. 依序按一下 `Generate API definition template` (產生 API 定義範本)、`Save`，以建立稍後要修改的範本定義。
  1. 記下您的 `API definition URL` 和 `key`
1. [設定 CI/CD](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements)
2. 修改 `swagger.json` (在位於 `\site\wwwroot\.azurefunctions\swagger\swagger.json` 的原始檔控制中)

現在，對於您存放庫中 `swagger.json` 的變更會裝載在您的函數應用程式，位在步驟 1.3 所記下的 `API definition URL` 和 `key` 中

## <a name="next-steps"></a>後續步驟
* [入門教學課程](functions-api-definition-getting-started.md)
  * 請嘗試我們的逐步解說，以查看 OpenAPI 定義實際運作！
* [Azure Functions GitHub 存放庫](https://github.com/Azure/Azure-Functions/)
  * 查看 Functions GitHub 以提供我們 API 定義支援預覽的意見反應！ 針對您想在後續更新看到的任何功能提出 GitHub 問題。
* [Azure Functions 開發人員參考](functions-reference.md)  
  * 可供程式設計人員撰寫函數程式碼及定義觸發程序和繫結時參考。

