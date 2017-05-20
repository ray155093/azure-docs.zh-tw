---
title: "在 Azure 中建立由佇列訊息所觸發的函式 | Microsoft Docs"
description: "使用 Azure Functions 來建立無伺服器函式，並讓此函式由提交至 Azure 儲存體佇列的訊息來叫用。"
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0e2501b0eb218d3c8a62dd4959b08ff85ec565eb
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="add-messages-to-an-azure-storage-queue-using-functions"></a>使用 Functions 在 Azure 儲存體佇列中新增訊息

在 Azure Functions 中，輸入和輸出繫結會提供宣告式方法，以便從函式連線到外部服務資料。 在本主題中，請學習如何新增會將訊息傳送至 Azure 佇列儲存體的輸出繫結，以更新現有函式。  

![檢視記錄中的訊息。](./media/functions-integrate-storage-queue-output-binding/functions-integrate-storage-binding-in-portal.png)

完成本主題中的所有步驟需要不到 5 分鐘的時間。

## <a name="prerequisites"></a>必要條件 

[!INCLUDE [Previous topics](../../includes/functions-quickstart-previous-topics.md)]

您還需要下載並安裝 [Microsoft Azure 儲存體總管](http://storageexplorer.com/)。 

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="add-binding"></a>新增輸出繫結
 
1. 展開函式應用程式和函式。

2. 按一下 [整合] 和 [+ 新輸出]，然後依序按一下 [Azure 佇列儲存體] 和 [選取]。
    
    ![在 Azure 入口網站中對函式新增佇列儲存體輸出繫結。](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding.png)

3. 使用表格中指定的設定，並按一下 [儲存]： 

    ![在 Azure 入口網站中對函式新增佇列儲存體輸出繫結。](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding-2.png)

    | 設定      |  建議的值   | 說明                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **佇列名稱**   | myqueue-items    | 儲存體帳戶中的連線目標佇列名稱。 |
    | **儲存體帳戶連線** | AzureWebJobStorage | 您可以使用應用程式函式已在使用的儲存體帳戶連線，或建立新的連線。  |
    | **訊息參數名稱** | outQueueItem | 輸出繫結參數的名稱。 | 

您已定義了輸出繫結，接下來您需要將程式碼更新為使用繫結來對佇列新增訊息。  

## <a name="update-the-function-code"></a>更新函式程式碼

1. 按一下函式以在編輯器中顯示函式程式碼。 

2. 若為 C# 函式，請依下面的方式更新函式定義，以新增 **outQueueItem** 儲存體繫結參數。 若為 JavaScript 函式，請略過此步驟。

    ```cs   
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, 
        ICollector<string> outQueueItem, TraceWriter log)
    {
        ....
    }
    ```

3. 在方法傳回前，對函式新增下列程式碼。 請使用您的函式語言所適用的程式碼片段。

    ```javascript
    context.bindings.outQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ```cs
    outQueueItem.Add("Name passed to the function: " + name);     
    ```

4. 按一下 [儲存] 來儲存變更。

傳遞至 HTTP 觸發程序的值會包含在新增至佇列的訊息中。
 
## <a name="test-the-function"></a>測試函式 

1. 儲存程式碼的變更後，按一下 [執行]。 

    ![在 Azure 入口網站中對函式新增佇列儲存體輸出繫結。](./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png)

2. 檢查記錄以確定函式已成功。 您第一次使用輸出繫結時，Functions 執行階段會在儲存體帳戶中建立名為 **outqueue** 的新佇列。

接下來，您可以連線到儲存體帳戶，以便驗證新佇列和您對佇列新增的訊息。 

## <a name="connect-to-the-queue"></a>連線至佇列

如果您已經安裝儲存體總管並將它連線至儲存體帳戶，請略過前三個步驟。    

1. 在您的函式中，按一下 [整合] 和新的 **Azure 佇列儲存體**輸出繫結，然後展開 [文件]。 複製**帳戶名稱**和**帳戶金鑰**。 您會使用這些認證來連線至儲存體帳戶。
 
    ![取得儲存體帳戶的連線認證。](./media/functions-integrate-storage-queue-output-binding/function-get-storage-account-credentials.png)

2. 執行 [Microsoft Azure 儲存體總管](http://storageexplorer.com/)工具，按一下左側的 [連線] 圖示，選擇 [使用儲存體帳戶名稱和金鑰]，然後按 [下一步]。

    ![執行「儲存體帳戶總管」工具。](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-1.png)
    
3. 輸入從步驟 1 得到的 [帳戶名稱] 和 [帳戶金鑰]，按 [下一步]，然後按一下 [連線]。 
  
    ![輸入儲存體認證和連線。](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-2.png)

4. 展開連結的儲存體帳戶，以滑鼠右鍵按一下 [佇列]，並確認有名為 **myqueue-items** 的佇列存在。 您也應該會看到佇列中已有訊息。  
 
    ![建立儲存體佇列。](./media/functions-integrate-storage-queue-output-binding/function-queue-storage-output-view-queue.png)
 

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>後續步驟

您已在現有函式中新增輸出繫結。 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

如需佇列儲存體繫結的詳細資訊，請參閱 [Azure Functions 儲存體佇列繫結](functions-bindings-storage-queue.md)。 




