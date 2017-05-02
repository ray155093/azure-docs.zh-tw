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
ms.date: 04/18/2017
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: d4354546f3342d65353a86a4cec7d02547ab92e7
ms.lasthandoff: 04/22/2017


---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>建立由 GitHub Webhook 所觸發的函式

了解如何建立 GitHub Webhook 所觸發的函式。 

![在 Azure 入口網站中建立函式應用程式](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

本主題需要[從 Azure 入口網站建立您的第一個函式](functions-create-first-azure-function.md)主題中所建立的資源。

您也需要 GitHub 帳戶。 如果您沒有帳戶，您可以[註冊免費的 GitHub 帳戶](https://github.com/join)。 

完成本主題中的所有步驟需要不到 5 分鐘的時間。

## <a name="find-your-function-app"></a>尋找應用程式函式    

1. 登入 [Azure 入口網站](https://portal.azure.com/)。 

2. 在入口網站頂端的搜尋列中，輸入函式應用程式的名稱並從清單中加以選取。

## <a name="create-function"></a>建立 GitHub Webhook 觸發的函式

1. 在函式應用程式中，按一下 [函式] 旁的 **+** 按鈕，按一下您所要語言的 [GitHubWebHook] 範本，然後按一下 [建立]。
   
    ![在 Azure 入口網站中建立 GitHub Webhook 觸發的函式。](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger.png) 

2. 按一下 [</> 取得函式 URL]，然後複製並儲存值。 對 [</> 取得 GitHub 祕密] 執行相同的動作。 您可使用這些值在 GitHub 中設定 Webhook。 

    ![檢閱函式程式碼](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png) 
         
接下來，您會在 GitHub 存放庫中建立 Webhook。 

## <a name="configure-the-webhook"></a>設定 Webhook
1. 在 GitHub 中，瀏覽至您自己的存放庫。 您也可以使用已分歧的任何存放庫。
 
2. 按一下 [設定]，然後按一下 [Webhook] 和 [新增 Webhook]。
   
    ![新增 GitHub webhook](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

3. 將函式的 URL 和密碼貼到 [承載 URL] 和 [密碼] 中，然後針對 [內容類型] 選取 [應用程式/json]。

4. 按一下 [讓我選擇個別事件]，選取 [問題註解]，然後按一下 [新增 Webhook]。
   
    ![設定 webhook URL 和密碼](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

現在，GitHub Webhook 已設定成在新增問題註解時觸發您的函式。 

## <a name="test-the-function"></a>測試函式
1. 在 GitHub 存放庫的新瀏覽器視窗中開啟 [問題] 索引標籤。

2. 在新視窗中，按一下 [新增問題]，輸入標題，然後按一下 [提交新問題]。 

2. 在問題中輸入註解，然後按一下 [註解] 。 

3. 在其他 GitHub 視窗中，按一下新 Webhook 旁邊的 [編輯]，向下捲動至 [最近的傳遞]，並確認您的函式已處理 Webhook 要求。 
 
    ![設定 webhook URL 和密碼](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-triggered.png)

   您的函式回應應包含 `New GitHub comment: <Your issue comment text>`。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


