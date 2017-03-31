---
title: "對您的第一個 Azure 邏輯應用程式建立工作流程 | Microsoft Docs"
description: "開始使用您的第一個邏輯應用程式連接應用程式和 SaaS 服務"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/25/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 221f1b9f0985bbaf0553f6ca01f0f048b9976315
ms.lasthandoff: 03/28/2017


---
# <a name="create-a-new-logic-app-connecting-saas-services"></a>建立連接 SaaS 服務的新邏輯應用程式
本主題只花幾分鐘就能示範如何開始使用 [Azure Logic Apps](logic-apps-what-are-logic-apps.md)。 我們將逐步說明可讓您將有趣的推文傳送至您的電子郵件的簡單工作流程。

若要使用此案例，您需要：

* Azure 訂用帳戶
* Twitter 帳戶
* Outlook.com 或 Office 365 Outlook 帳戶

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>建立新的邏輯應用程式，以電子郵件傳送您的推文

1. 登入 [Azure 入口網站](https://portal.azure.com)。 

2. 從左側功能表中，選擇 [新增] > [企業整合] > [邏輯應用程式]。

    您也可以選擇 [新增]，然後在搜尋方塊中輸入 `logic app`，接著按 Enter。 選擇 [邏輯應用程式] > [建立]。

3. 輸入邏輯應用程式的名稱、選取您的 Azure 訂用帳戶、建立或選取 Azure 資源群組、選取位置，然後選擇 [建立]。

    如果您選取 [釘選到儀表板]，邏輯應用程式會在部署後自動開啟。

4. 第一次開啟邏輯應用程式時，您可以從要啟動的範本進行選取。
現在按一下 [空白邏輯應用程式]  ，從頭開始建置。 

5. 您所需建立的第一個項目是觸發程序。 這是會啟動邏輯應用程式的事件。 在搜尋方塊中搜尋 **twitter**，然後選取 [有新推文張貼時]。 使用 Twitter 帳戶的使用者名稱和密碼進行登入。

6. 現在輸入搜尋詞彙來觸發邏輯應用程式。

   ![Twitter 搜尋](media/logic-apps-create-a-logic-app/twittersearch.png)

    [頻率] 和 [間隔] 會決定邏輯應用程式檢查新推文並傳回該時間範圍內所有推文的頻率。

7. 選取 [新步驟]，然後選擇 [新增動作] 或 [新增條件]。

    當您選取 [新增動作] 時，您可以搜尋[可用的連接器](../connectors/apis-list.md)以選擇動作。 

8. 在搜尋方塊中搜尋 **outlook**，然後選取 [傳送電子郵件] 將電子郵件從 Outlook 帳戶傳送到任何指定的電子郵件地址。

   ![動作](media/logic-apps-create-a-logic-app/actions.png)

9. 您現在必須針對您要的電子郵件填寫參數：

   ![參數](media/logic-apps-create-a-logic-app/parameters.png)

10. 最後，您可以選取 [儲存]  ，以存留您的邏輯應用程式。

## <a name="manage-your-logic-app-after-creation"></a>在建立後管理邏輯應用程式

現在，您的邏輯應用程式已啟動並執行。 它會以輸入的搜尋詞彙定期檢查推文。 當它找到符合的推文時，就會傳送郵件給您。 最後，您將看到如何停用應用程式，或看到它的執行狀況。

1. 移至 [Azure 入口網站](https://portal.azure.com)。

2. 在左側功能表上，按一下 [更多服務]。 在 [企業整合] 底下選擇 [Logic Apps]。 選取您的邏輯應用程式。

    *    若要檢視應用程式的狀態、執行記錄和一般資訊，請在邏輯應用程式的功能表上，選擇 [概觀]。 如果找不到您預期的資料，請在命令列上選擇 [重新整理]。

    *    若要編輯您的應用程式，請在邏輯應用程式的功能表上，選擇 [邏輯應用程式設計工具]。

    *    若要暫時關閉應用程式，請在邏輯應用程式的功能表上，選擇 [概觀]。 在命令列上選擇 [停用]。

    *    若要刪除應用程式，請在邏輯應用程式的功能表上，選擇 [概觀]。 
    在命令列上選擇 [刪除]。 輸入邏輯應用程式的名稱，然後選擇 [刪除]。

不到 5 分鐘的時間，您便能夠設定在雲端中執行的簡單邏輯應用程式。 若要深入了解如何使用邏輯應用程式功能，請參閱 [使用邏輯應用程式功能]。 若要深入了解邏輯應用程式定義本身，請參閱 [撰寫邏輯應用程式定義](../logic-apps/logic-apps-author-definitions.md)。

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[使用邏輯應用程式功能]: logic-apps-create-a-logic-app.md
