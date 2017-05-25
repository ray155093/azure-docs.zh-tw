---
title: "在雲端應用程式與雲端服務之間建立第一個工作流程 - Azure Logic Apps | Microsoft Docs"
description: "在 Azure Logic Apps 中建立及執行工作流程，以自動執行系統整合和企業應用程式整合 (EAI) 案例的商務程序"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
keywords: "工作流程, 雲端應用程式, 雲端服務, 商務程序, 系統整合, 企業應用程式整合, EAI"
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/31/2017
ms.author: LADocs; jehollan; estfan
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 204bf123509729b60b55c306050cef54aa7fecc5
ms.contentlocale: zh-tw
ms.lasthandoff: 05/17/2017

---

# <a name="create-your-first-logic-app-workflow-to-automate-processes-between-cloud-apps-and-cloud-services"></a>建立第一個邏輯應用程式工作流程來自動執行雲端應用程式與雲端服務之間的程序

不需撰寫任何程式碼，您可以在建立並執行工作流程時利用 [Azure Logic Apps](logic-apps-what-are-logic-apps.md)，更輕鬆快速地自動執行商務程序。 第一個範例示範如何建立基本邏輯應用程式工作流程，以檢查網站上新內容的 RSS 摘要。 當新項目出現在網站的摘要時，邏輯應用程式會從 Outlook 或 Gmail 帳戶傳送電子郵件。

若要建立並執行邏輯應用程式，您需要下列項目︰

* Azure 訂用帳戶。 如果您沒有訂用帳戶，您可以[開始使用免費 Azure 帳戶](https://azure.microsoft.com/free/)。 否則，您可以[註冊隨用隨付訂用帳戶](https://azure.microsoft.com/pricing/purchase-options/)。

  您的 Azure 訂用帳戶用於邏輯應用程式使用量計費。 了解 Azure Logic Apps 的[使用量計量](../logic-apps/logic-apps-pricing.md)和[計價](https://azure.microsoft.com/pricing/details/logic-apps)方式。

此外，這個範例需要下列項目︰

* Outlook.com、Office 365 Outlook 或 Gmail 帳戶

    > [!TIP]
    > 如果您有個人 [Microsoft 帳戶](https://account.microsoft.com/account)，您便有 Outlook.com 帳戶。 否則，如果您有 Azure 工作或學校帳戶，您便有 **Office 365 Outlook** 帳戶。

* 網站的 RSS 摘要連結。 這個範例會使用 [CNN.com 網站的頭條報導 RSS 摘要](http://rss.cnn.com/rss/cnn_topstories.rss)：`http://rss.cnn.com/rss/cnn_topstories.rss`

## <a name="add-a-trigger-that-starts-your-workflow"></a>新增可啟動工作流程的觸發程序

[*觸發程序*](./logic-apps-what-are-logic-apps.md#logic-app-concepts)是啟動邏輯應用程式工作流程的事件，而且是邏輯應用程式所需的第一個項目。

1. 登入 [Azure 入口網站](https://portal.azure.com "Azure 入口網站")。

2. 從左側功能表中，選擇 [新增] > [企業整合] > [邏輯應用程式]，如下所示：

     ![Azure 入口網站, 新增, 企業整合, 邏輯應用程式](media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

   > [!TIP]
   > 您也可以選擇 [新增]，然後在搜尋方塊中輸入 `logic app`，接著按 Enter。 然後選擇 [邏輯應用程式] > [建立]。

3. 為您的邏輯應用程式命名並選取您的 Azure 訂用帳戶。 現在建立或選取 Azure 資源群組，以協助您組織及管理相關的 Azure 資源。 最後，選取用於裝載您的邏輯應用程式的資料中心位置。 當您準備就緒，選擇 [釘選到儀表板]，然後選擇 [建立]。

     ![邏輯應用程式詳細資料](media/logic-apps-create-a-logic-app/logic-app-settings.png)

   > [!NOTE]
   > 當您選取 [釘選到儀表板] 時，邏輯應用程式會在部署後出現於 Azure 儀表板，並自動開啟。 如果邏輯應用程式未出現在儀表板上，請在 [所有資源] 圖格上選擇 [更多資訊]，然後選取您的邏輯應用程式。 在左側功能表上，選擇 [更多服務]。 在 [企業整合] 底下，選擇 [Logic Apps]，然後選取您的邏輯應用程式。

4. 當您第一次開啟邏輯應用程式時，邏輯應用程式設計工具會顯示您可開始使用的範本。 暫時選擇 [空白邏輯應用程式]，以便從頭建置邏輯應用程式。

    邏輯應用程式設計工具隨即開啟，並顯示您可以在邏輯應用程式中使用的服務和「觸發程序」。

5. 在搜尋方塊中輸入 `RSS`，然後選取此觸發程序︰**RSS - 摘要項目發佈時** 

    ![RSS 觸發程序](media/logic-apps-create-a-logic-app/rss-trigger.png)

6. 輸入您想要追蹤之網站的 RSS 摘要連結。 

     您也可以變更 [頻率] 和 [間隔]。 
     這些設定會決定邏輯應用程式檢查新項目並傳回該時間範圍內找到之所有項目的頻率。

     例如，讓我們每天檢查張貼至 CNN 網站的頭條報導。

     ![使用 RSS 摘要、頻率和間隔設定觸發程序](media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

7. 儲存您目前的工作。 (在設計工具命令列上選擇 [儲存]。)

   ![儲存您的邏輯應用程式](media/logic-apps-create-a-logic-app/save-logic-app.png)

   當您儲存時，邏輯應用程式會上線，但邏輯應用程式目前只會檢查指定之 RSS 摘要中的新項目。 
   為了讓此範例更加實用，我們新增了邏輯應用程式在觸發程序引發後執行的動作。

## <a name="add-an-action-that-responds-to-your-trigger"></a>新增可回應觸發程序的動作

[動作](./logic-apps-what-are-logic-apps.md#logic-app-concepts)是邏輯應用程式工作流程所執行的工作。 將觸發程序新增至邏輯應用程式之後，您可以新增一個動作，以對該觸發程序所產生的資料執行作業。 在我們的範例中，我們現在新增一個動作，以在新項目出現於網站的 RSS 摘要時傳送電子郵件。

1. 在設計工具中，於您的觸發程序下選擇 [新增步驟] > **[新增動作]**，如下所示︰

   ![新增動作](media/logic-apps-create-a-logic-app/add-new-action.png)

   設計工具會顯示[可用的連接器](../connectors/apis-list.md)，因此您可以選取要在觸發程序引發時執行的動作。

2. 根據您的電子郵件帳戶，遵循 Outlook 或 Gmail 的步驟。

   * 若要從 Outlook 帳戶傳送電子郵件，請在搜尋方塊中輸入 `outlook`。 在 [服務] 之下，針對個人 Microsoft 帳戶選擇 [Outlook.com]，或針對 Azure 工作或學校帳戶選擇 [Office 365 Outlook]。 
   在 [動作] 之下，選取 [傳送電子郵件]。

       ![選取 Outlook「傳送電子郵件」動作](media/logic-apps-create-a-logic-app/actions.png)

   * 若要從 Gmail 帳戶傳送電子郵件，請在搜尋方塊中輸入 `gmail`。 
   在 [動作] 之下，選取 [傳送電子郵件]。

       ![選擇「Gmail - 傳送電子郵件」](media/logic-apps-create-a-logic-app/actions-gmail.png)

3. 當系統提示您輸入認證時，使用您電子郵件帳戶的使用者名稱和密碼登入。 

4. 提供此動作的詳細資料 (如目的地電子郵件地址)，然後選擇要納入電子郵件中資料的參數，例如︰

   ![選取要納入電子郵件中的資料](media/logic-apps-create-a-logic-app/rss-action-setup.png)

    因此如果您選擇 Outlook，邏輯應用程式可能如此範例所示︰

    ![已完成的邏輯應用程式](media/logic-apps-create-a-logic-app/save-run-complete-logic-app.png)

5.    儲存您的變更。 (在設計工具命令列上選擇 [儲存]。)

6. 您現在可以手動執行邏輯應用程式進行測試。 在設計工具命令列上選擇 [執行]。 否則，您可以讓邏輯應用程式根據您設定的排程，檢查指定的 RSS 摘要。

   如果邏輯應用程式找到新的項目，則邏輯應用程式會傳送電子郵件，其中包含您選取的資料。 
   如果找不到任何新項目，邏輯應用程式會略過傳送電子郵件的動作。

7. 若要監視及檢查邏輯應用程式的執行和觸發歷程記錄，請在邏輯應用程式功能表上選擇 [概觀]。

   ![監視及檢視邏輯應用程式執行和觸發歷程記錄](media/logic-apps-create-a-logic-app/logic-app-run-trigger-history.png)

   > [!TIP]
   > 如果找不到您預期的資料，請嘗試在命令列上選擇 [重新整理]。

   若要深入了解邏輯應用程式的狀態或執行和觸發歷程記錄，或診斷應用程式邏輯，請參閱[針對邏輯應用程式進行疑難排解](logic-apps-diagnosing-failures.md)。

      > [!NOTE]
      > 邏輯應用程式會繼續執行，直到您關閉應用程式為止。 若要暫時關閉應用程式，請在邏輯應用程式功能表上選擇 [概觀]。 在命令列上選擇 [停用]。

恭喜，您剛設定並執行第一個基本邏輯應用程式。 您也了解如何輕鬆地建立可自動執行程序的工作流程，並整合雲端應用程式和雲端服務 - 全都不需要程式碼。

## <a name="manage-your-logic-app"></a>管理邏輯應用程式

若要管理您的應用程式，您可以執行一些工作，如檢查狀態、編輯、檢視歷程記錄、關閉或刪除邏輯應用程式。

1. 登入 [Azure 入口網站](https://portal.azure.com "Azure 入口網站")。

2. 在左側功能表上，選擇 [更多服務]。 在 [企業整合] 底下選擇 [Logic Apps]。 選取您的邏輯應用程式。 

   在邏輯應用程式功能表中，您可以找到下列邏輯應用程式管理工作︰

   |Task|步驟| 
   |:---|:---| 
   | 檢視應用程式的狀態、執行歷程記錄和一般資訊| 選擇 [概觀]。| 
   | 編輯應用程式 | 選擇 [邏輯應用程式設計工具]。 | 
   | 檢視應用程式的工作流程 JSON 定義 | 選擇 [邏輯應用程式程式碼檢視]。 | 
   | 檢視在邏輯應用程式上執行的作業 | 選擇 [活動記錄]。 | 
   | 檢視邏輯應用程式過去的版本 | 選擇 [版本]。 | 
   | 暫時關閉應用程式 | 選擇 [概觀]，然後在命令列上選擇 [停用]。 | 
   | 刪除應用程式 | 選擇 [概觀]，然後在命令列上選擇 [刪除]。 輸入邏輯應用程式的名稱，然後選擇 [刪除]。 | 

## <a name="get-help"></a>取得說明

若要提出問題、回答問題以及了解其他 Azure Logic Apps 使用者的做法，請造訪 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

若要改善 Azure Logic Apps 和連接器，請在 [Azure Logic Apps 使用者意見反應網站](http://aka.ms/logicapps-wish)上票選或提交想法。

## <a name="next-steps"></a>後續步驟

*  [新增條件並執行工作流程](../logic-apps/logic-apps-use-logic-app-features.md)
*     [邏輯應用程式範本](../logic-apps/logic-apps-use-logic-app-templates.md)
*  [從 Azure Resource Manager 範本建立邏輯應用程式](../logic-apps/logic-apps-arm-provision.md)

