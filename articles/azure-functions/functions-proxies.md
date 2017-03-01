---
title: "在 Azure Functions 中使用 Proxy | Microsoft Docs"
description: "如何使用 Azure Functions Proxy 的概觀"
services: functions
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/19/2017
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: facdd2abbb90a85535b8936ea655a824134c4c8b
ms.openlocfilehash: 3e8b8f9908cf24baf7a5d70521c79dbd470001f8
ms.lasthandoff: 02/22/2017


---
# <a name="working-with-azure-functions-proxies-preview"></a>使用 Azure Functions Proxy (預覽)

> [!Note] 
> Azure Functions Proxy 目前為預覽版。 預覽版是免費的，但是標準 Functions 計費會套用至 Proxy 執行。 請參閱 [Azure Functions 價格](https://azure.microsoft.com/pricing/details/functions/)。

這篇文章說明如何設定與使用 Azure Functions Proxy。 這項功能可讓您在您的函式應用程式上指定端點，該端點是由其他資源所實作。 您可以使用這些 Proxy 將大型 API 細分為多個函式應用程式 (如同在微服務架構中)，同時仍為用戶端呈現單一的 API 介面。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]


## <a name="a-nameenableaenabling-azure-functions-proxies"></a><a name="enable"></a>啟用 Azure Functions Proxy

預設不會啟用 Proxy。 功能停用時您可以建立 Proxy，但是它們不會執行。 下列步驟說明如何啟用 Proxy：

1. 開啟 [Azure 入口網站]，然後瀏覽至您的函式應用程式。
2. 選取 [函式應用程式設定]。
3. 將 [啟用 Azure Functions Proxy (預覽)] 切換為 [開啟]。

您也可以在新功能可供使用時，返回這裡以更新 Proxy 執行階段。


## <a name="creating-a-proxy"></a>建立 Proxy

本節將示範如何在 Functions 入口網站中建立 Proxy。

1. 開啟 [Azure 入口網站]，然後瀏覽至您的函式應用程式。
2. 在左側導覽中，選取 [新增 Proxy]。
3. 為您的 Proxy 提供名稱。
4. 設定在此函式應用程式上公開的端點，方法是指定**路由範本**和 **HTTP 方法**。 這些參數的行為會根據 [HTTP 觸發程序]的規則
5. 將**後端 URL** 設定為其他端點。 這可能是另一個函式應用程式中的函式，或者它可能是任何其他 API。
6. 按一下 [建立]。

您的 Proxy 現在會存在做為函式應用程式上的新端點。 從用戶端的觀點而言，它相當於 Azure Functions 中的 HttpTrigger。 您可以藉由複製 Proxy URL 並使用最愛的 HTTP 用戶端來測試它，以嘗試您的新 Proxy。


## <a name="a-namemodify-requestsamodifying-backend-requests"></a><a name="modify-requests"></a>修改後端要求

後端 URL 參數不需要是靜態的。 您可以在要求或應用程式設定的輸入上對它設定條件。


### <a name="using-request-parameters"></a>使用要求參數

在路由範本中使用的參數，可能會做為後端 URL 屬性的輸入。 值會由名稱參考，括在大括號 "{}" 中。

例如，如果 Proxy 有像是 `/pets/{petId}` 的路由範本，後端 URL 可以包含 `{petId}` 的值，如同 `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`。 如果路由範本以萬用字元終止，例如 `/api/{*restOfPath}`，值 `{restOfPath}` 是連入要求之其餘路徑區段的字串表示。


### <a name="using-application-settings"></a>使用應用程式設定

您也可以參考[應用程式設定](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#develop)，方法是在設定名稱周圍加上百分比符號 '%'。

例如，`https://%ORDER_PROCESSING_HOST%/api/orders` 的後端 URL 會將 "%ORDER_PROCESSING_HOST%" 取代為 ORDER_PROCESSING_HOST 設定的值。

> [!TIP] 
> 當您有多個部署或測試環境時，使用後端主機的應用程式設定。 這樣一來，您可以確保您永遠與該環境的正確後端通訊。



## <a name="deployment-methods"></a>部署方法

您設定的 Proxy 會儲存在 proxies.json 檔案中，位於函式應用程式目錄的根目錄中。 當使用 Functions 支援的任何[部署方法](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment)時，您可以手動編輯此檔案，並且部署它做為應用程式的一部分。

必須啟用此功能才能處理檔案。 您可以依照[啟用 Azure Functions Proxy](#enable) 中的指示來執行此操作。

Proxies.json 是由 Proxy 物件定義，該物件包含具名 Proxy 及其定義。 範例檔案如下所示：

```json
{
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

在上述範例中，每個 Proxy 有好記的名稱，例如「proxy1」。 對應的 Proxy 定義物件是由下列屬性定義︰

* **matchCondition**︰必要項 - 物件，會定義將會觸發此 Proxy 執行的要求。 它包含與 [HTTP 觸發程序]共用的兩個屬性：
    * _methods_：這是 Proxy 將會回應的 HTTP 方法陣列。 如果未指定，Proxy 將會回應路由上的所有 HTTP 方法。
    * _route_：必要項 - 這會定義路由範本，控制 Proxy 將回應哪些要求 URL。 不同於 HTTP 觸發程序，這個項目沒有預設值。
* **backendUri**︰要求應該要對其進行 Proxy 的後端資源 URL。 此值可能已樣板化，如[修改後端要求](#modify-requests)中所述。 如果不包含這個屬性，Azure Functions 會回應 HTTP 200 OK。

> [!Note] 
> 路由屬性 Azure Functions Proxy 不接受 Functions 主機組態的 routePrefix 屬性。 如果您想要包含前置詞，例如 /api，它必須包含在路由屬性中。



[Azure 入口網站]: https://portal.azure.com
[HTTP 觸發程序]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#http-trigger
