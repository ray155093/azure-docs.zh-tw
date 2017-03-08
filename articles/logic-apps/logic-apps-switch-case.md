---
title: "在 Azure Logic Apps 中使用 Switch 陳述式 | Microsoft Docs"
description: "Switch 陳述式可讓您根據 Logic Apps 中的運算式值輕鬆地採取不同的動作"
services: logic-apps
documentationcenter: dev-center-name
manager: erikre
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: 3a028507f9bbf15c8fd52ccc7c22a5763a9b1b3e
ms.openlocfilehash: 284cfca17b5abf785f7af9569c518c4400fe36fd
ms.lasthandoff: 02/14/2017


---
# <a name="use-switch-statement-in-logic-apps"></a>在 Logic Apps 中使用 Switch 陳述式
當製作工作流程時，您通常需要根據物件或運算式的值採取不同的動作。 例如，您可能會根據 HTTP 要求的狀態碼或核准電子郵件的選取選項，使邏輯應用程式行為有所不同。

這些案例可透過使用 Switch 陳述式來完成︰邏輯應用程式會評估權杖或運算式，並選擇具有執行動作之相同值的案例。 只有一種情況應該符合 Switch 陳述式。

 > [!TIP]
 > 如同所有的程式設計語言，Switch 陳述式僅支援等號比較運算子。 如果您需要其他關係運算子 (例如，大於)，請使用條件陳述式。
 >
 > 若要確認確定性執行行為，案例必須包含唯一且靜態的值，而不是動態的權杖或運算式。

## <a name="prerequisites"></a>必要條件

- 作用中 Azure 訂用帳戶。
    - 如果您沒有作用中 Azure 訂用帳戶，[請建立免費帳戶](https://azure.microsoft.com/free/)，或免費嘗試 [Logic Apps](https://tryappservice.azure.com/)。
- [Logic Apps 基本知識](logic-apps-what-are-logic-apps.md)。

## <a name="working-with-switch-statement-in-designer"></a>使用設計工具中的 Switch 陳述式
若要示範如何使用 Switch 陳述式，讓我們建立一個可監視上傳至 Dropbox 檔案的邏輯應用程式。 邏輯應用程式會傳送出核准電子郵件，以判斷是否應該傳送到 SharePoint。 視核准者選取的值而定，我們將使用 Switch 陳述式來採取不同的動作。

1. 開始建立邏輯應用程式，然後選取 [Dropbox - 建立檔案時] 觸發程序。

 ![使用 Dropbox - 建立檔案時觸發程序](./media/logic-apps-switch-case/dropbox-trigger.jpg)

2. 使用 **Outlook.com - 傳送核准電子郵件**動作追蹤觸發程序。

 > [!TIP]
 > 邏輯應用程式也支援來自 Office 365 Outlook 帳戶的核准電子郵件案例。

 - 如果您沒有現有的連線，系統會提示您建立一個。
 - 填寫必要的欄位中，我們將傳送電子郵件傳送給 approvers@contoso.com。
 - 在 [使用者選項] 下，輸入 `Approve, Reject`。

 ![設定連線](./media/logic-apps-switch-case/send-approval-email-action.jpg)

3. 新增 Switch 陳述式。
 - 選取 [+ 新步驟]、**...更多**，[新增 Switch 陳述式]。
 - 我們想要根據傳送核准電子郵件動作的 `SelectedOptions` 輸出選取要執行的項目，您可以在 [新增動態內容] 選取器找到它。
 - 當使用者選取 `Approve` 時，使用案例 1 處理。
    - 一經核准，使用 **SharePoint Online - 建立檔案**動作將原始檔案複製到 SharePoint Online。
    - 在案例中新增另一個動作來通知使用者 SharePoint 上有可用的新檔案。
 - 當使用者選取 `Reject` 時，新增另一個案例來處理。
    - 如果已拒絕，傳送通知電子郵件通知其他核准者會拒絕檔案，且不需要任何進一步的動作。
 - 我們知道 `SelectedOptions` 僅有兩個提供的選項，預設案例可以保留空白。

 ![Switch 陳述式](./media/logic-apps-switch-case/switch.jpg)

 > [!NOTE]
 > Switch 陳述式需要至少一個預設案例以外的案例。

4. 在 Switch 陳述式之後，使用 **Dropbox - 刪除檔案**動作來刪除上傳至 Dropbox 的原始檔案。

5. 儲存您的邏輯應用程式，並藉由將檔案上傳至 Dropbox 加以測試。 您稍後應該很快會收到核准電子郵件，選取一個選項，並觀察行為。
 > [!TIP]
 > 請查看如何[監視 Logic Apps](logic-apps-monitor-your-logic-apps.md)。

## <a name="understanding-code-behind"></a>了解程式碼後置
現在您已使用 Switch 陳述式成功建立邏輯應用程式。 讓我們看看程式碼後置，如下所示。

```json
"Switch": {
    "type": "Switch",
    "expression": "@body('Send_approval_email')?['SelectedOption']",
    "cases": {
        "Case 1" : {
            "case" : "Approved",
            "actions" : {}
        },
        "Case 2" : {
            "case" : "Rejected",
            "actions" : {}
        }
    },
    "default": {
        "actions": {}
    },
    "runAfter": {
        "Send_approval_email": [
            "Succeeded"
        ]
    }
}
```

`"Switch"` 是 Switch 陳述式的名稱，它可以重新命名以增加可讀性。 `"type": "Switch"` 指出動作是 Switch 陳述式。 `"expression"`，在此案例中，使用者的選取選項，會針對稍後在定義中宣告的每個案例進行評估。 `"cases"` 可以包含任何數目的案例，如果沒有符合 Switch 運算式的案例，則會執行 `"default"` 內的動作。

`"cases"` 內可以有任意數目的案例。 針對每個案例，`"Case 1"` 是案例的名稱，它可以重新命名以增加可讀性。 `"case"` 會指定 Switch 運算式會與之比較的案例標籤，其必須為常數且唯一的值。  

## <a name="next-steps"></a>後續步驟
- 嘗試其他 [Logic Apps 功能](logic-apps-use-logic-app-features.md)。
- 深入了解[錯誤和例外狀況處理](logic-apps-exception-handling.md)。
- 深入探討[工作流程語言功能](logic-apps-author-definitions.md)。
- 留下包含您問題或意見反應的意見，或是[告訴我們，我們要如何改善 Logic Apps](https://feedback.azure.com/forums/287593-logic-apps)。
