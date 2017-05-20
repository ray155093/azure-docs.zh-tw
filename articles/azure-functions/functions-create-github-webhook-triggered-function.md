---
title: "在 GitHub Webhook 所觸發的 Azure 中建立函式 | Microsoft Docs"
description: "使用 Azure Functions 建立 GitHub Webhook 所叫用的無伺服器函式。"
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 34988ef05a27062ca109a1640e39695b52b8773f
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>建立由 GitHub Webhook 所觸發的函式

了解如何使用 GitHub 專屬的承載來建立由 HTTP Webhook 要求所觸發的函式。 

![Azure 入口網站中由 GitHub Webhook 所觸發的函式](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

完成本主題中的所有步驟需要不到 5 分鐘的時間。

## <a name="prerequisites"></a>必要條件 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

您還需要一個 GitHub 帳戶，且帳戶中至少要有一個專案。 如果您沒有帳戶，您可以[註冊免費的 GitHub 帳戶](https://github.com/join)。

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="create-function"></a>建立 GitHub Webhook 觸發的函式

1. 展開函式應用程式，按一下 [函式] 旁的 **+** 按鈕，然後按一下您所要語言的 [GitHubWebHook] 範本。 **為您的函式命名**，然後按一下 [建立]。 

2. 在您的新函式中，按一下 [</> 取得函式 URL]，然後複製並儲存值。 對 [</> 取得 GitHub 祕密] 執行相同的動作。 您可使用這些值在 GitHub 中設定 Webhook。 

    ![檢閱函式程式碼](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png) 
         
接下來，您會在 GitHub 存放庫中建立 Webhook。 

## <a name="configure-the-webhook"></a>設定 Webhook
1. 在 GitHub 中，瀏覽至您自己的存放庫。 您也可以使用已分歧的任何存放庫。 如果您需要將存放庫分岔，請使用 <https://github.com/Azure-Samples/functions-quickstart>。 
 
2. 按一下 [設定]，然後按一下 [Webhook] 和 [新增 Webhook]。
   
    ![新增 GitHub webhook](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

3. 使用資料表中指定的設定，然後按一下 [新增 Webhook]。
 
    ![設定 webhook URL 和密碼](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

    | 設定      |  建議的值   | 說明                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **承載 URL** | 複製的值 | 使用 **</> 取得函式 URL** 所傳回的值。 |
    | **祕密**   | 複製的值 | 使用 **</> 取得 GitHub 祕密**所傳回的值。 |
    | **內容類型** | application/json | 函式預期使用 JSON 承載。 |
    | 事件觸發程序 | 讓我選取個別事件 | 我們只想對問題註解事件來觸發。  |
    |                | 問題註解                    |  |

現在，GitHub Webhook 已設定成在新增問題註解時觸發您的函式。 

## <a name="test-the-function"></a>測試函式
1. 在 GitHub 存放庫的新瀏覽器視窗中開啟 [問題] 索引標籤。

2. 在新視窗中，按一下 [新增問題]，輸入標題，然後按一下 [提交新問題]。 

2. 在問題中輸入註解，然後按一下 [註解] 。 

    ![新增 GitHub 問題註解。](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-add-comment.png) 

3. 返回入口網站，並檢視記錄。 您應該會看到具有新註解文字的追蹤項目。 
    
     ![檢視記錄中的註解文字。](./media/functions-create-github-webhook-triggered-function/function-app-view-logs.png)
 

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>後續步驟

您已建立函式，此函式會在收到 GitHub Webhook 所提出的要求時開始執行。 
[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]如需 Webhook 觸發程序的詳細資訊，請參閱 [Azure Functions HTTP 和 webhook 繫結](functions-bindings-http-webhook.md)。 




