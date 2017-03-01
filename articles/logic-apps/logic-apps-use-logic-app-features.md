---
title: "新增條件式邏輯和啟動工作流程 - Azure Logic Apps | Microsoft Docs"
description: "新增條件式邏輯、觸發程序、動作和參數，以控制 Azure Logic Apps 中工作流程的執行方式。"
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e4e24de4-049a-4b3a-a14c-3bf3163287a8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2017
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: 9f7d623ec213de6d46f59547aff9d4417ac95ede
ms.openlocfilehash: 41aafe94d24f0e22fe2256ab213c7668b670764c
ms.lasthandoff: 02/15/2017


---
# <a name="use-logic-apps-features"></a>使用 Logic Apps 功能
在 [上一個主題](../logic-apps/logic-apps-create-a-logic-app.md)中，您已建立第一個邏輯應用程式。 現在，您將使用 Azure Logic Apps來建置更完整的程序。 本主題將介紹下列新的 Azure Logic Apps 概念：

* 條件式邏輯，只有在符合特定條件時，才會執行動作。
* 用以編輯現有邏輯應用程式的程式碼檢視。
* 啟動工作流程的選項。

在完成本主題之前，您應先完成 [建立新的邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)中的步驟。 在 [Azure 入口網站]中，瀏覽至您的邏輯應用程式，然後按一下摘要中的 [觸發程序和動作]，以編輯邏輯應用程式定義。

## <a name="reference-material"></a>參考資料
您可能會發現下列文件很有用：

* [管理和執行階段 REST API](https://msdn.microsoft.com/library/azure/mt643787.aspx) - 包括如何直接叫用邏輯應用程式
* [語言參考](https://msdn.microsoft.com/library/azure/mt643789.aspx) - 所有支援的函式/運算式完整清單
* [觸發程序和動作類型](https://msdn.microsoft.com/library/azure/mt643939.aspx) - 不同類型的動作及其採用的輸入
* [App Service 概觀](../app-service/app-service-value-prop-what-is.md) - 說明建置方案時可選擇哪些元件

## <a name="add-conditional-logic-to-your-logic-app"></a>將條件式邏輯新增至邏輯應用程式

雖然您的邏輯應用程式原始流程能夠運作，但我們可在某些方面予以改善。

### <a name="conditional"></a>條件式

您的第一個邏輯應用程式可能會導致您收到太多電子郵件。 下列步驟會新增條件式邏輯，如此您就只有在推文是來自有特定數量粉絲的某人時，才會收到電子郵件。

0. 在邏輯應用程式設計工具中，選擇 [新步驟 (+)] > [新增動作]。
0.    尋找並新增適用於 Twitter 的 [取得使用者] 動作。
0. 若要取得 Twitter 使用者的相關資訊，從觸發程序尋找並新增 [推文者] 欄位。

    ![取得使用者](media/logic-apps-use-logic-app-features/getuser.png)

0. 選擇 [新步驟 (+)] > [新增條件]。
0. 若要篩選使用者擁有的粉絲數量，在 [物件名稱] 下方，選擇 [新增動態內容]。 
0.    在 [搜尋] 方塊中，尋找並新增 [粉絲計數] 欄位。
0. 在 [關聯性] 下方，選取 [大於]。
0. 在 [值] 方塊中，輸入您希望使用者擁有的粉絲數量。

    ![條件式](media/logic-apps-use-logic-app-features/conditional.png)

0. 最後，將 [傳送電子郵件] 方塊拖放到 [如果是] 方塊中。 

現在您只有在粉絲計數符合您的條件時，才會收到電子郵件。

## <a name="repeat-actions-over-a-list-with-foreach"></a>使用 forEach 對清單重複執行動作

forEach 迴圈會指定要用來重複執行某動作的陣列。 如果它不是陣列，流程便會失敗。 舉例來說，如果 action1 會輸出訊息陣列，而您想要傳送每則訊息，則可以在動作的屬性中包含這個 forEach 陳述式︰`forEach : "@action('action1').outputs.messages"`

## <a name="edit-the-code-definition-for-a-logic-app"></a>編輯邏輯應用程式的程式碼定義

儘管您擁有邏輯應用程式設計工具，您還是可以直接編輯定義邏輯應用程式的程式碼。

1. 在命令列中，選擇 [程式碼檢視]。

    隨即會開啟完整的編輯器，並顯示您所編輯的定義。

    ![程式碼檢視](media/logic-apps-use-logic-app-features/codeview.png)

    在文字編輯器中，您可以在相同的邏輯應用程式或邏輯應用程式之間複製並貼上任何數量的動作。 
    您也可以輕鬆地在定義中新增或移除整個區段，以及與其他人共用定義。

2. 若要儲存您的編輯，請選擇 [儲存]。

### <a name="parameters"></a>參數

某些 Logic Apps 功能只能在程式碼檢視中取得，例如參數。 參數可讓您輕鬆地在整個邏輯應用程式中重複使用值。 例如，如果您想要在數個動作中使用同一個電子郵件地址，您應將該電子郵件地址定義為參數。

參數很適合用來提取您很可能經常變更的值。 當您需要在不同環境中覆寫參數時，參數特別有用。 如需如何根據環境覆寫參數的詳細資訊，請參閱我們的 [REST API 文件](https://docs.microsoft.com/rest/api/logic)。

這個範例示範如何更新現有的邏輯應用程式，讓您可以針對查詢字詞使用參數。

1. 在程式碼檢視中，尋找 `parameters : {}` 物件，並新增主題物件：

        "topic" : {
            "type" : "string",
            "defaultValue" : "MicrosoftAzure"
        }

2. 移至 `twitterconnector` 動作、尋找查詢值，然後使用 `#@{parameters('topic')}` 取代該值。 

    若要加入兩或多個字串，您也可以使用 `concat` 函式。 
    例如，`@concat('#',parameters('topic'))` 的運作方式與上述相同。

3.    完成之後，請選擇 [儲存]。 

    現在，每小時都會有回推數超過五個的新推文傳遞到您 Dropbox 中名為 [推文] 的資料夾。

若要深入了解邏輯應用程式定義，請參閱 [撰寫邏輯應用程式定義](../logic-apps/logic-apps-author-definitions.md)。

## <a name="start-logic-app-workflows"></a>啟動邏輯應用程式工作流程

您有數個不同的選項可用來啟動您邏輯應用程式中定義的工作流程。 您一律可在 [Azure 入口網站]中隨選啟動工作流程。

### <a name="recurrence-triggers"></a>循環觸發程序

循環觸發程序會依照您指定的間隔執行。 當觸發程序具有條件式邏輯時，觸發程序會判斷工作流程是否需要執行。 觸發程序透過傳回 `200` 狀態碼，來指示工作流程應該執行。 當工作流程不需要執行時，觸發程序會傳回 `202` 狀態碼。

### <a name="callback-using-rest-apis"></a>使用 REST API 回呼

若要啟動工作流程，服務可以呼叫邏輯應用程式端點。 若要隨選啟動該種邏輯應用程式，請選擇命令列上的 [立即執行]。 請參閱[呼叫邏輯應用程式端點做為觸發程序來啟動工作流程](../logic-apps/logic-apps-http-endpoint.md)。 

<!-- Shared links -->
[Azure 入口網站]: https://portal.azure.com

