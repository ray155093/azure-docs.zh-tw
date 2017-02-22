---
title: SMTP | Microsoft Docs
description: "使用 Azure App Service 建立邏輯應用程式。 連接到 SMTP 以傳送電子郵件。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: 3a0fdef111fbd4a9f7491e247f2236cf70b89dca


---
# <a name="get-started-with-the-smtp-connector"></a>開始使用 SMTP 連接器
連接到 SMTP 以傳送電子郵件。

若要使用[任何連接器](apis-list.md)，您必須先建立邏輯應用程式。 您可以從[立即建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)來開始。

## <a name="connect-to-smtp"></a>連接到 SMTP
您必須先建立與服務的連線，才能透過邏輯應用程式存取任何服務。 [連線](connectors-overview.md)可讓邏輯應用程式與另一個服務連線。 例如，若要連接到 SMTP，您必須先有 SMTP *連線*。 若要建立連線，您需要提供平常用來存取所要連線之服務的認證。 因此，在 SMTP 範例中，您需要連接名稱、SMTP 伺服器位址，以及使用者登入資訊的認證才能建立 SMTP 連線。 [深入了解連線]()  

### <a name="create-a-connection-to-smtp"></a>建立至 SMTP 的連線
> [!INCLUDE [Steps to create a connection to SMTP](../../includes/connectors-create-api-smtp.md)]
> 
> 

## <a name="use-an-smtp-trigger"></a>使用 SMTP 觸發程序
觸發程序是可用來啟動邏輯應用程式中所定義之工作流程的事件。 [深入了解觸發程序](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。

在此範例中，由於 SMTP 沒有自己的觸發程序，因此我們將使用 **Salesforce - 當物件建立時**觸發程序。 當 Salesforce 中有新的物件建立時，觸發程序就會啟動。 在範例中，我們會設定使其每次在 Salesforce 中建立新的潛在客戶時，系統會透過 SMTP 連接器執行*傳送電子郵件*的動作，並附帶已建立新潛在客戶的通知。

1. 在邏輯應用程式設計工具的搜尋方塊中輸入 *salesforce*，然後選取 [Salesforce - 當建立物件時] 觸發程序。  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  
2. [當建立物件時]  控制項隨即顯示。
   ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  
3. 選取 [物件類型] 然後從清單的物件中選取 [潛在客戶]。 在此步驟中，您會指出您要建立一個觸發程序，此觸發程序將在每次 Salesforce 中有建立新潛在客戶的情況時，通知您的邏輯應用程式。  
   ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  
4. 觸發程序已建立。  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## <a name="use-an-smtp-action"></a>使用 SMTP 動作
動作是由邏輯應用程式中定義的工作流程所執行的作業。 [深入了解動作](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。

現在已新增觸發程序之後，請遵循下列步驟新增 Salesforce 中有新的潛在客戶建立時會發生的 SMTP 動作。

1. 選取 [+ 新的步驟]，來新增您想要在建立新的潛在客戶時採取的動作。  
   ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  
2. 選取 [新增動作] 。 這會開啟搜尋方塊，您可以在其中搜尋任何想要採取的動作。  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  
3. 輸入 *smtp* 以搜尋與 SMTP 相關的動作。  
4. 選取 [SMTP - 傳送電子郵件]，做為建立新的潛在客戶時要採取的動作。 動作控制區塊便會開啟。 如果您先前未曾在設計工具區塊中建立 SMTP 連線，您必須這麼做。  
   ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)    
5. 在 [SMTP - 傳送電子郵件] 區塊中輸入所需的電子郵件資訊。  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  
6. 儲存您的工作以啟動工作流程。  

## <a name="technical-details"></a>技術詳細資訊
以下是有關這個連接支援的觸發程序、動作和回應的詳細資料︰

## <a name="smtp-triggers"></a>SMTP 觸發程序
SMTP 沒有觸發程序。 

## <a name="smtp-actions"></a>SMTP 動作
SMTP 具有下列動作︰

| 動作 | 說明 |
| --- | --- |
| [傳送電子郵件](connectors-create-api-smtp.md#send-email) |這樣作業會將電子郵件傳送給一或多位收件者。 |

### <a name="action-details"></a>動作詳細資料
以下是此連接器的動作以及其回應的詳細資料︰

### <a name="send-email"></a>傳送電子郵件
這樣作業會將電子郵件傳送給一或多位收件者。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 收件人 |收件人 |指定電子郵件地址，並以分號分隔，例如 recipient1@domain.com;recipient2@domain.com |
| CC |副本 |指定電子郵件地址，並以分號分隔，例如 recipient1@domain.com;recipient2@domain.com |
| 主旨 |主旨 |電子郵件主旨 |
| 內文 |body |電子郵件內文 |
| 從 |從 |寄件者的電子郵件地址，例如 sender@domain.com |
| IsHtml |是 HTML |以 HTML 格式傳送電子郵件 (true/false) |
| 密件副本 |密件副本 |指定電子郵件地址，並以分號分隔，例如 recipient1@domain.com;recipient2@domain.com |
| 重要性 |重要性 |電子郵件的重要性 (高、一般或低) |
| ContentData |附件內容資料 |內容資料 (base64 編碼用於資料流，以及現狀用於字串) |
| ContentType |附件內容類型 |內容類型 |
| ContentTransferEncoding |附件內容轉移編碼 |內容轉移編碼 (base64 或無) |
| FileName |附件檔案名稱 |檔案名稱 |
| ContentId |附件內容識別碼 |內容識別碼 |

* 表示這是必要屬性

## <a name="http-responses"></a>HTTP 回應
上述動作和觸發程序可以傳回一或多個下列的 HTTP 狀態碼︰ 

| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 202 |已接受 |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤。 |
| 預設值 |作業失敗。 |

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


