---
title: 邏輯應用程式 webhook 連接器 | Microsoft Docs
description: 執行如篩選陣列等動作的 Webhook 動作和觸發程序概觀。
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2016
ms.author: jehollan

---
# 開始使用 webhook 連接器
透過使用 webhook 和觸發程序，您可以觸發、暫停和繼續流程來達成下列目的︰

* 一收到項目即從 [Azure 事件中樞](https://github.com/logicappsio/EventHubAPI)觸發
* 等待核准再繼續工作流程

若要了解如何建立支援 webhook 訂閱的 API，請參閱[建立邏輯應用程式連接器](../app-service-logic/app-service-logic-create-api-app.md)一文。

- - -
## 使用 webhook 觸發程序
觸發程序是可用來啟動邏輯應用程式中所定義之工作流程的事件。[深入了解觸發程序](connectors-overview.md)。Webhook 觸發程序特別有用，因為它不依賴輪詢新的項目- 就像[要求觸發程序](connectors-native-reqres.md)一樣，邏輯應用程式會引發事件發生的瞬間。它的運作方式是註冊服務的*回呼 URL*，就能視需要用來觸發邏輯應用程式。

以下是如何在邏輯應用程式設計工具中設定 HTTP 觸發程序的範例順序。這是假設您已部署或正在存取的 API 會遵循[邏輯應用程式中使用的 webhook 訂閱和取消訂閱模式](../app-service-logic/app-service-logic-create-api-app.md#webhook-triggers)。每當邏輯應用程式隨新的 webhook 儲存或由停用切換為啟用時，就會進行訂閱呼叫。每當邏輯應用程式 webhook 觸發程序移除與儲存時，就會進行取消訂閱呼叫。

1. 將 **HTTP Webhook** 觸發程序新增為邏輯應用程式中的第一個步驟
2. 填入 webhook 訂閱和取消訂閱呼叫的參數
   
   * 以上所遵循的模式與 [HTTP 動作](connectors-native-http.md)格式相同
     
     ![HTTP 觸發程序](./media/connectors-native-webhook/using-trigger.png)
3. 加入至少一個動作
4. 按一下 [儲存] 以發佈邏輯應用程式 - 這樣會呼叫含有觸發此邏輯應用程式所需的回呼 URL 的訂閱端點
5. 每當服務對回呼 URL 發出 `HTTP POST` 呼叫時，邏輯應用程式就會觸發 (並包含要求中所傳遞的任何資料)

## 使用 webhook 動作
動作是由邏輯應用程式中定義的工作流程所執行的作業。[深入了解動作。](connectors-overview.md) Webhook 動作特別有用，因為它會註冊服務的*回呼 URL*，並等到呼叫該 URL 後再繼續。[傳送核准電子郵件](connectors-create-api-office365-outlook.md)是遵循這個模式的連接器範例。您可以透過 webhook 動作將此模式擴充到任何服務。這是假設您已部署或正在存取的 API 會遵循[邏輯應用程式中使用的 webhook 訂閱和取消訂閱模式](../app-service-logic/app-service-logic-create-api-app.md#webhook-actions)。每當邏輯應用程式執行 webhook 動作時，就會進行訂閱呼叫。每當執行在等待回應時取消或在邏輯應用程式執行逾時前，就會進行取消訂閱呼叫。

新增 webhook 動作：

1. 選取 [新增步驟] 按鈕
2. 選擇 [新增動作]
3. 在動作搜尋方塊中，輸入 "webhook" 以列出 **HTTP Webhook** 動作
   
    ![選取查詢動作](./media/connectors-native-webhook/using-action-1.png)
4. 填入 webhook 訂閱和取消訂閱呼叫的參數
   
   * 以上所遵循的模式與 [HTTP 動作](connectors-native-http.md)格式相同
     
     ![完整查詢動作](./media/connectors-native-webhook/using-action-2.png)
   * 在執行階段，邏輯應用程式會在抵達步驟後呼叫訂閱端點
5. 按一下工具列左上角的 [儲存]，邏輯應用程式便會儲存並發佈 (啟動)

- - -
## 技術詳細資訊
以下是 webhook 支援的觸發程序和動作的詳細資料。

## WebHook 觸發程序
觸發程序是啟動工作流程的作業。[深入了解觸發程序。](connectors-overview.md) 此連接器有 1 個觸發程序。

| 動作 | 說明 |
| --- | --- |
| HTTP Webhook |訂閱服務的回呼 URL，就能視需要呼叫該 URL 以觸發邏輯應用程式。 |

### 觸發程序詳細資料
webhook 連接器隨附 1 個可能的觸發程序。下面是關於動作、其必要和選擇性輸入欄位，以及與其使用方式相關聯的對應輸出詳細資料的資訊。

#### HTTP Webhook
訂閱服務的回呼 URL，就能視需要呼叫該 URL 以觸發邏輯應用程式。* 代表必要欄位。

| 顯示名稱 | 屬性名稱 | 說明 |
| --- | --- | --- |
| 訂閱方法* |方法 |用於訂閱要求的 HTTP 方法 |
| 訂閱 URI* |uri |用於訂閱要求的 HTTP URI |
| 取消訂閱方法* |方法 |用於取消訂閱要求的 HTTP 方法 |
| 取消訂閱 URI* |uri |用於取消訂閱要求的 HTTP URI |
| 訂閱本文 |body |訂閱的 HTTP 要求本文 |
| 訂閱標頭 |headers |訂閱的 HTTP 要求標頭 |
| 訂閱驗證 |驗證 |訂閱要使用的 HTTP 驗證。如需詳細資訊，請參閱 [HTTP 連接器](connectors-native-http.md#authenication) |
| 取消訂閱頁面 |body |取消訂閱的 HTTP 要求本文 |
| 取消訂閱標頭 |headers |取消訂閱的 HTTP 要求標頭 |
| 取消訂閱驗證 |驗證 |取消訂閱要使用的 HTTP 驗證。如需詳細資訊，請參閱 [HTTP 連接器](connectors-native-http.md#authenication) |

<br>

**輸出詳細資料**

Webhook 要求

| 屬性名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| 標頭 |物件 |Webhook 要求標頭 |
| 內文 |物件 |Webhook 要求物件 |
| 狀態碼 |int |Webhook 要求狀態碼 |

## Webhook 動作
動作是由邏輯應用程式中定義的工作流程所執行的作業。[深入了解動作。](connectors-overview.md) 連接器有 1 個可能的動作。

| 動作 | 說明 |
| --- | --- |
| HTTP Webhook |訂閱服務的回呼 URL，就能視需要呼叫繼續工作流程步驟的 URL。 |

### 動作詳細資料
webhook 連接器隨附 1 個可能的動作。下面是關於動作、其必要和選擇性輸入欄位，以及與其使用方式相關聯的對應輸出詳細資料的資訊。

#### HTTP Webhook
訂閱服務的回呼 URL，就能視需要呼叫繼續工作流程步驟的 URL。* 代表必要欄位。

| 顯示名稱 | 屬性名稱 | 說明 |
| --- | --- | --- |
| 訂閱方法* |方法 |用於訂閱要求的 HTTP 方法 |
| 訂閱 URI* |uri |用於訂閱要求的 HTTP URI |
| 取消訂閱方法* |方法 |用於取消訂閱要求的 HTTP 方法 |
| 取消訂閱 URI* |uri |用於取消訂閱要求的 HTTP URI |
| 訂閱本文 |body |訂閱的 HTTP 要求本文 |
| 訂閱標頭 |headers |訂閱的 HTTP 要求標頭 |
| 訂閱驗證 |驗證 |訂閱要使用的 HTTP 驗證。如需詳細資訊，請參閱 [HTTP 連接器](connectors-native-http.md#authentication) |
| 取消訂閱頁面 |body |取消訂閱的 HTTP 要求本文 |
| 取消訂閱標頭 |headers |取消訂閱的 HTTP 要求標頭 |
| 取消訂閱驗證 |驗證 |取消訂閱要使用的 HTTP 驗證。如需詳細資訊，請參閱 [HTTP 連接器](connectors-native-http.md#authentication) |

<br>

**輸出詳細資料**

Webhook 要求

| 屬性名稱 | 資料類型 | 說明 |
| --- | --- | --- |
| 標頭 |物件 |Webhook 要求標頭 |
| 內文 |物件 |Webhook 要求物件 |
| 狀態碼 |int |Webhook 要求狀態碼 |

- - -
## 後續步驟
以下是有關如何推動邏輯應用程式的詳細資料和我們的社群。

## 建立邏輯應用程式
立即試用平台和[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。您可以查看我們的 [API 清單](apis-list.md)，以探索邏輯應用程式中其他可用的連接器。

<!---HONumber=AcomDC_0727_2016-->