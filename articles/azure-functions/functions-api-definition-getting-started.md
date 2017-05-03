---
title: "開始使用 Azure Functions 中的 OpenAPI 中繼資料 | Microsoft Docs"
description: "開始使用 Azure Functions 中的 OpenAPI 支援"
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
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: ad82c1a552d9d77259c44b938975eebc44933b86
ms.lasthandoff: 04/26/2017


---
# <a name="creating-openapi-20-swagger-metadata-for-a-function-app-preview"></a>建立函數應用程式的 OpenAPI 2.0 (Swagger) 中繼資料 (預覽)

此文件會逐步引導您針對裝載在 Azure Functions 上的簡單 API 建立 OpenAPI 定義。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

### <a name="what-is-openapi-swagger"></a>OpenAPI (Swagger) 是什麼？
[Swagger 中繼資料](http://swagger.io/)是定義 API 的功能和運作模式的檔案，並且讓函數裝載的 REST API 可供其他各種軟體使用。 Microsoft 提供的 PowerApps 和 [API Apps](https://docs.microsoft.com/azure/app-service-api/app-service-api-dotnet-get-started#a-idcodegena-generate-client-code-for-the-data-tier) 等，和 [Postman](https://www.getpostman.com/docs/importing_swagger) 等第三方開發人員工具，以及[許多套件](http://swagger.io/tools/)，都可讓您的 API 透過 Swagger 定義來使用。

## <a name="prepare-function"></a>建立包含簡單 API 的函數
  若要建立 OpenAPI 定義，我們必須先建立包含簡單 API 的函數。 如果您已經有裝載在函數應用程式的 API，則您可以直接跳至下一節
1. 建立新的函數應用程式。
  1. [Azure 入口網站](https://portal.azure.com) > `+ New` > 搜尋「函數應用程式」
1. 在新的函數應用程式中建立新的 HTTP 觸發程序函數
  1. 系統已經將定義非常簡單之 REST API 的程式碼，預先填入您的函數。
  1. 任何以查詢參數或在主體中傳遞到函數的字串，都會傳回 "Hello {輸入}"
1. 移至新 HTTP 觸發程序函數的 `Integrate` 索引標籤
  1. 將 `Allowed HTTP methods` 切換成 `Selected methods`
  1. 在 `Selected HTTP methods` 中，取消選取除了 POST 之外的所有動詞。
    ![選取的 HTTP 方法](./media/functions-api-definition-getting-started/selectedHTTPmethods.png)
  1. 此步驟日後會簡化您的 API 定義。

## <a name="enable"></a>啟用 API 定義支援
1. 瀏覽至 `your function name` > `API Definition (preview)`
1. 將 `API Definition Source` 設定為 `Function`
  1. 此步驟會為您的函數應用程式啟用一組 OpenAPI 選項，包含裝載來自您函數應用程式網域之 OpenAPI 檔案的端點、[OpenAPI 編輯器 (英文)](http://editor.swagger.io) 的內嵌複本，以及快速入門定義產生器。
![已啟用的定義](./media/functions-api-definition-getting-started/enabledefinition.png)

## <a name="create-definition"></a>從範本建立 API 定義
1. 按一下 `Generate API Definition template`
  1. 此步驟會掃描您的函數應用程式以尋找 HTTP 觸發程序函數，並使用 functions.json 中的資訊來產生 OpenAPI 文件。
2. 將作業物件新增到 `paths: /api/yourfunctionroute post:`
  1. 快速入門 OpenAPI 文件是完整 OpenAPI 文件的大綱。 它需要更多中繼資料以成為完整的 OpenAPI 定義，例如作業物件和回應範本。
  1. 下列作業物件範例有填好的 produces/consumes 區段、參數物件和回應物件。
  
  ```yaml
      post:
        operationId: /api/yourfunctionroute/post
        consumes: [application/json]
        produces: [application/json]
        parameters:
          - name: name
            in: body
            description: Your name
            x-ms-summary: Your name
            required: true
            schema:
              type: object
              properties:
                name:
                  type: string
        description: >-
          Replace with Operation Object
          #http://swagger.io/specification/#operationObject
        responses:
          '200':
            description: A Greeting
            x-ms-summary: Your name
            schema:
              type: string
        security:
          - apikeyQuery: []
  ```

> [!NOTE]
>  x-ms-summary 提供 Logic Apps、Flow 和 PowerApps 中的顯示名稱。
>
> 如需詳細資訊，請參閱[為 PowerApps 自訂 Swagger 定義](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/)。

3. 按一下 `save` 來儲存您的變更 ![新增範本定義](./media/functions-api-definition-getting-started/addingtemplate.png)

## <a name="use-definition"></a>使用 API 定義
1. 複製您的 `API definition URL` 並貼上到新的瀏覽器索引標籤，以檢視原始 OpenAPI 文件。
1. 您可以使用該 URL，將 OpenAPI 文件匯入到任意數目的工具來測試及整合。
  1. 許多 Azure 資源都能使用儲存在函數應用程式設定中的 API 定義 URL，來自動匯入 OpenAPI 文件。 作為 `Function` API 定義來源的一部分，我們會為您更新該 URL。


## <a name="test-definition"></a>使用 Swagger UI 測試 API 定義
內嵌 API 定義編輯器的 UI 檢視中內建測試工具。 新增您的 API 金鑰，然後使用每個方法下的 `Try this operation` 按鈕。 該工具會使用您的 API 定義來格式化要求，而成功的回應表示您的定義正確。

### <a name="steps"></a>步驟：

1. 複製函數 API 金鑰
  1. API 金鑰可以在您的 HTTP 觸發程序函數的 `function name` > `Keys` > `Function Keys`
   之下找到![函數金鑰](./media/functions-api-definition-getting-started/functionkey.png)
1. 瀏覽到 `API Definition (preview)` 頁面。
  1. 按一下 `Authenticate`，並將您的函數 API 金鑰新增到頂端的安全性物件。
  ![OpenAPI 金鑰](./media/functions-api-definition-getting-started/definitionTest auth.png)
1. 選取 `/api/yourfunctionroute` > `POST`
1. 按一下 `Try it out` 並輸入要測試的名稱
1. 您應該會在 `Pretty` 下看到測試結果「成功」
![API 定義測試](./media/functions-api-definition-getting-started/definitionTest.png)

## <a name="next-steps"></a>後續步驟
* [函數中的 OpenAPI 定義概觀](functions-api-definition.md)
  * 如需更多 OpenAPI 支援的資訊，請閱讀完整的文件。
* [Azure Functions 開發人員參考](functions-reference.md)  
  * 可供程式設計人員撰寫函數程式碼及定義觸發程序和繫結時參考。
* [Azure Functions GitHub 存放庫](https://github.com/Azure/Azure-Functions/)
  * 查看 Functions GitHub 以提供我們 API 定義支援預覽的意見反應！ 針對您想在後續更新看到的任何功能提出 GitHub 問題。

