---
title: "在 Azure 中建置無伺服器的社交媒體儀表板 | Microsoft Docs"
description: "在 Azure 中建置無伺服器的社交媒體儀表板"
services: functions, logic-apps, cognitive-services
keywords: "工作流程, 雲端應用程式, 雲端服務, 商務程序, 系統整合, 企業應用程式整合, EAI"
documentationcenter: 
author: rick-anderson
manager: wpickett
editor: 
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: riande
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0d3eb2af197e9923d8e4a86bf1a0033f61e3c568
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---

# <a name="building-a-serverless-social-media-dashboard-in-azure"></a>在 Azure 中建置無伺服器的社交媒體儀表板

[Azure Functions](functions-overview.md) 與 [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md) 整合，可讓您與其他 Azure 和第三方服務之間建置複雜的協調流程。 本主題示範如何從社交媒體摘要觸發邏輯應用程式，並使用 [Azure 辨識服務](../cognitive-services/Welcome.md)分析文字。

本文示範如何在 Azure 入口網站中建立具有下列功能的邏輯應用程式：

> [!div class="checklist"]
> * 使用您提供的關鍵字或雜湊標記檢查新推文。
> * 使用**偵測情感**連接器判斷推文情感 (從不良到良好)。
> * 使用 Azure 函式將推文情感分為三類 (RED、YELLOW 或 GREEN - 代表不良、中性和良好)。
> * 使用條件來檢查情感是否為 RED (不良)。
> * 如果條件為 RED，則會傳送電子郵件。

下圖顯示設計工具中的邏輯應用程式局部︰

![此圖顯示邏輯應用程式設計工具中之應用程式的前 2 個步驟](media/functions-twitter-email/designer1.png)

## <a name="prerequisites"></a>必要條件

* 一個 Azure 帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。
* Twitter 帳戶。

## <a name="create-a-function-app"></a>建立函數應用程式
 
[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal2.md)]

### <a name="create-a-categorize-function"></a>建立分類函式

當函式應用程式部署完成後，開啟新的函式應用程式。 在本節中，您將建立函式將推文情感分為三類 (RED、YELLOW 或 GREEN - 代表不佳、中性和良好)。

![函式應用程式刀鋒視窗，Functions +](media/functions-twitter-email/add_fun.png)

保留預設的 **Webhook + API**、**CSharp**，然後選取 [建立這個函式]。

![函式應用程式刀鋒視窗，Functions +](media/functions-twitter-email/add_fun2.png)

您建立 Webhook/API (也就是 HTTP 觸發程序) 函式，供您建置的應用程式視需要呼叫。 如果您想建立根據排程執行的函式，您需要建立計時器函式。

用下列程式碼取代 *run.csx* 檔案的內容：

```c#
using System.Net;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    string category = "GREEN";

    // Get request body.
    double score = await req.Content.ReadAsAsync<double>();

    if (score < .3)
    {
        category = "RED";
    }
    else if (score < .6)
    {
        category = "YELLOW";
    }

    return req.CreateResponse(HttpStatusCode.OK, category);
}
```

儲存變更。

### <a name="test-the-function"></a>測試函式

選取 [測試] (在程式碼方塊右邊)。  在 [要求本文] 文字方塊中輸入 0.2，然後選取 [執行]。 輸出會顯示 "RED"，HTTP 狀態為 200 OK。

 ![test ](media/functions-twitter-email/test.png)

## <a name="cognitive-services"></a>辨識服務

建立辨識服務帳戶。 需要有辨識服務帳戶，才能偵測我們所監視之推文的情感。

瀏覽至 [新增 > 智慧 + 分析 > 辨識服務]。 設定每個必要欄位︰

![[建立辨識帳戶] 刀鋒視窗](media/functions-twitter-email/cog_svcs_account.png)

| 欄位               | 範例值 | 註解 |
| ----------------- | ------------ | ------------- |
| 帳戶名稱 | MyCognitiveServicesAccnt | 輸入唯一名稱。 |
| API 類型 | 文字分析 API | 選取文字分析 |
| 位置 | 美國西部 | 目前只有 [美國西部] 可用 |
| 定價層 | F0 | 如果您用完呼叫，請設定為較高層次。|
| 資源群組 | rg1 | 使用您先前指定的資源群組。|

### <a name="copy-the-cognitive-services-key"></a>複製辨識服務金鑰

選取 [金鑰]。 您在稍後的步驟中需要金鑰。

 ![之間的信任](media/functions-twitter-email/keys.png)

## <a name="create-a-logic-app"></a>建立邏輯應用程式

在 Azure 入口網站中，按一下 [新增 > 企業整合 > 邏輯應用程式]。

![上一步之後的新增邏輯應用程式步驟](media/functions-twitter-email/new_logicApp.png)

在 [建立邏輯應用程式] 刀鋒視窗中，輸入每個欄位，然後選取 [建立]。

![上一步之後的建立邏輯應用程式步驟](media/functions-twitter-email/new_logicApp2.png)

建立邏輯應用程式之後，它會在設計工具中開啟。 選取 [空白邏輯應用程式] 範本。

![空白邏輯應用程式](media/functions-twitter-email/blank.png)

## <a name="add-a-trigger-to-twitter"></a>將觸發程序新增至 twitter

[邏輯應用程式設計工具] 會顯示您可以連線的許多服務和觸發程序。

選取 [Twitter] 服務。

![twitter 連接器](media/functions-twitter-email/twitter_connector.png)

選取觸發程序 [當有新推文張貼時]。

![當有新推文張貼時觸發程序](media/functions-twitter-email/tw_trig.png)

登入您的 twitter 帳戶。

![登入您的 twitter 帳戶。](media/functions-twitter-email/signin_twit.png)

輸入您的密碼，然後選取 [授權應用程式]。

![上一步之後在新視窗中驗證 twitter](media/functions-twitter-email/auth_twit.png)

輸入搜尋文字、頻率和間隔。 如果您指定熱門的雜湊標記 (例如 #football、#soccer 或 #futbol)，您可以快速使用辨識服務帳戶中已分配的所有服務呼叫。 如果用完呼叫，您可以增加定價層。 

每隔 15 分鐘搜尋一次 #Azure︰

![每隔 15 分鐘 #Azure](media/functions-twitter-email/azure_tweet.png)

儲存應用程式。

### <a name="add-a-text-analytics-connector"></a>新增 [文字分析] 連接器

文字分析連接器會偵測推文情感。

選取 [新增步驟]，然後選取 [新增動作]。

![選取 [新增步驟]，然後選取 [新增動作]](media/functions-twitter-email/new_step.png)

新增 [文字分析] 連接器。

![[選擇動作] 視窗](media/functions-twitter-email/choose_action.png)

選取 [偵測情感] 動作。 情感分級通常正確，但有時會誤解文字。

![偵測情感](media/functions-twitter-email/detect_sent.png)

### <a name="create-the-detect-sentiment-action"></a>建立「偵測情感」動作

  * 輸入連線名稱，例如 **MyKey**。
  * 複製並貼上您在[建立辨識服務帳戶](#cognitive-services)步驟中建立的金鑰。
  * 選取 [ **建立**]。
  * 儲存應用程式。

![偵測情感](media/functions-twitter-email/ta_detect_sent.png)

針對 [要分析的文字]，選取 [推文文字] 圖示。

![偵測情感](media/functions-twitter-email/ds_tta.png)

![偵測情感](media/functions-twitter-email/ds_tta2.png)

儲存應用程式。

## <a name="connect-to-the-azure-function"></a>連線至 Azure 函式

在本節中，您可以新增先前建立來將推文情感分類為 RED、YELLOW 或 GREEN 的函式。

* 在 Logic Apps 設計工具中，選取 [新增步驟]，然後選取 [新增動作]。
* 選取 [Azure Functions]。
* 選取 [選擇 Azure 函式]。

![顯示 [選擇 Azure 函式] 的 Azure 函式方塊](media/functions-twitter-email/choose_fun.png)

* 選取您先前建立的 Azure 函式。
* 選取 [分數] 填入 [要求本文] 中。

![分數](media/functions-twitter-email/trigger_score.png)

儲存應用程式。

## <a name="add-email-notification"></a>新增電子郵件通知

在本節中，我們針對負面情感推文新增條件檢查 (RED 條件)。

* 選取 [新增步驟]。
* 選取 [新增條件] 。
* 在第一個 [選擇值] 文字方塊中，選取 [本文]。
* 在第二個 [選擇值] 文字方塊中，輸入 "RED"。
* 儲存應用程式。

![條件方塊](media/functions-twitter-email/condition.png)

* 在 [如果是，不執行任何動作] 方塊中，選取 [新增動作]。
* 在 [搜尋所有服務和動作] 方塊中，輸入 Outlook 或 Gmail。 本教學課程中使用 Outlook。 如需 Gmail 的指示，請參閱 [新增 Gmail 動作] (../logic-apps/logic-apps-create-a-logic-app.md#add-an-action-that-responds-to-your-trigger)。 注意：如果您有個人的 [Microsoft 帳戶](https://account.microsoft.com/account)，則可用來作為 Outlook.com 帳戶。

![[選擇動作] 方塊](media/functions-twitter-email/outlook.png)

選取 [Outlook.com 傳送電子郵件]。

![Outlook.com 方塊](media/functions-twitter-email/sendEmail.png)

登入 Outlook.com。

![登入方塊](media/functions-twitter-email/signin_outlook.png)

輸入下列項目：

   * **收件者**︰訊息應該寄到的電子郵件。
   * **主旨**︰分數。
   * **本文**︰位置和推文文字。

![[傳送電子郵件] 方塊](media/functions-twitter-email/sendEmail2.png)

儲存應用程式。
選取 [執行] 啟動應用程式。

### <a name="check-the-status"></a>檢查狀態

在 [邏輯應用程式] 刀鋒視窗中，選取 [概觀]，然後在 [執行歷程記錄] 資料行中選取一個資料列︰

![[概觀] 刀鋒視窗](media/functions-twitter-email/over1.png)

下圖顯示條件不是 true 而未傳送電子郵件時的執行詳細資料。

![[概觀] 刀鋒視窗](media/functions-twitter-email/skipped.png)

如果想要立即測試 [傳送電子郵件] 函式︰

* 將第一個步驟 (**當有新推文張貼時**) 中的 [輸入] 變更為熱門詞彙，例如 #football、#soccer 或 #futbol。

處理熱門詞彙比冷門詞彙耗用更多資源。 在確認電子郵件可運作後，您可能想要變更搜尋詞彙。

下圖顯示條件為 true 且傳送電子郵件時的執行詳細資料。

![[概觀] 刀鋒視窗](media/functions-twitter-email/sent.png)

您可以選取任何服務方塊，以顯示用於執行之資料的詳細資訊。 選取 [當有新推文張貼時]，它會顯示搜尋文字及所有輸出 (包括我們未使用的輸出)。

## <a name="next-steps"></a>後續步驟

*  [Azure Functions 簡介](functions-overview.md)
*  [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)
*  [新增條件並執行工作流程](../logic-apps/logic-apps-use-logic-app-features.md)
*  [邏輯應用程式範本](../logic-apps/logic-apps-use-logic-app-templates.md)
*  [從 Azure Resource Manager 範本建立邏輯應用程式](../logic-apps/logic-apps-arm-provision.md)

## <a name="get-help"></a>取得說明

若要提出問題、回答問題以及了解其他 Azure Logic Apps 使用者的做法，請造訪 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

若要改善 Azure Logic Apps 和連接器，請在 [Azure Logic Apps 使用者意見反應網站](http://aka.ms/logicapps-wish)上票選或提交想法。

