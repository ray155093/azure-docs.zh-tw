---
title: "從 Azure Logic Apps 連線到內部部署檔案系統 | Microsoft Docs"
description: "透過內部部署資料閘道和檔案系統連接器，連線到邏輯應用程式工作流程中的內部部署檔案系統"
keywords: "檔案系統"
services: logic-apps
author: derek1ee
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2017
ms.author: LADocs; deli
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: f33e7c58103c57e17e4e273caba1ab9b83f0cd2b
ms.contentlocale: zh-tw
ms.lasthandoff: 07/06/2017

---

# <a name="connect-to-on-premises-file-systems-from-logic-apps-with-the-file-system-connector"></a>使用檔案系統連接器，從邏輯應用程式連線到內部部署檔案系統

混合式雲端連線的核心是邏輯應用程式，因此，若要管理資料，並安全地存取內部部署資源，則您的邏輯應用程式可以使用內部部署資料閘道。 在本文中，我們會以一個基本的案例來示範如何連線至內部部署檔案系統︰將已上傳至 Dropbox 的檔案複製到檔案共用，然後傳送電子郵件。

## <a name="prerequisites"></a>必要條件

- 安裝及設定最新的[內部部署資料閘道](https://www.microsoft.com/download/details.aspx?id=53127)。
- 安裝版本 1.15.6150.1 或更高版本的最新內部部署資料閘道。 [連接至內部部署資料閘道](http://aka.ms/logicapps-gateway)會列出步驟。 必須先在內部部署機器上安裝閘道，才能繼續執行其餘的步驟。

## <a name="add-trigger-and-actions-for-connecting-to-your-file-system"></a>新增用來連線到檔案系統的觸發程序和動作

1. 建立邏輯應用程式，並新增這個 Dropbox 觸發程序：**建立檔案時** 
2. 在觸發程序下方，選擇 [下一步] > [新增動作]。 
3. 在搜尋方塊中，輸入 `file system`，讓您可以檢視檔案系統連接器支援的所有動作。

   ![搜尋檔案連接器](media/logic-apps-using-file-connector/search-file-connector.png)

2. 選擇 [建立檔案] 動作，然後建立與您檔案系統的連線。

   如果您目前沒有連線，系統會提示您建立連線。

   1. 選擇 [透過內部部署資料閘道連線]。 隨即出現更多屬性。
   2. 選取檔案系統的根資料夾。
      
       > [!NOTE]
       > 根資料夾是主要的父資料夾，會作為所有檔案相關動作的相對路徑。 您可以指定內部部署資料閘道安裝所在電腦的本機資料夾，或者資料夾可以是電腦可存取的網路共用。

   3. 輸入閘道的使用者名稱和密碼。
   4. 選取您先前安裝的閘道。

       ![設定連線](media/logic-apps-using-file-connector/create-file.png)

3. 提供所有詳細資料之後，請選擇 [建立]。 

   Logic Apps 會設定並測試連線，以確定連線運作正常。 
   如果已正確設定連線，則會看到您先前所選取動作的選項。 
   檔案系統連接器現在已可供使用。

4. 指定您想要將檔案從 Dropbox 複製到您內部部署檔案共用的根資料夾。

   ![建立檔案動作](media/logic-apps-using-file-connector/create-file-filled.png)

5. 邏輯應用程式複製檔案之後，請新增傳送電子郵件的 Outlook 動作，讓相關使用者了解新的檔案。 輸入電子郵件的收件者、標題和本文。 

   在動態內容選取器中，您可以選擇來自檔案連接器的資料輸出，以將更多詳細資料新增至電子郵件。

   ![傳送電子郵件動作](media/logic-apps-using-file-connector/send-email.png)

6. 儲存您的邏輯應用程式。 將檔案上傳至 Dropbox，以測試應用程式。 檔案應該會複製到內部部署檔案共用，而且您應該會收到該作業的電子郵件。

   > [!TIP] 
   > 了解如何[監視邏輯應用程式](../logic-apps/logic-apps-monitor-your-logic-apps.md)。

恭喜，您現在已經有可運作的邏輯應用程式，可連線到內部部署檔案系統的。 請嘗試探索連接器所提供的其他功能，例如︰

- 建立檔案
- 列出資料夾中的檔案
- 附加檔案
- 刪除檔案
- 取得檔案內容
- 使用路徑來取得檔案內容
- 取得檔案中繼資料
- 使用路徑取得檔案中繼資料
- 列出根資料夾中的檔案
- 更新檔案

## <a name="view-the-swagger"></a>檢視 Swagger
請參閱 [Swagger 詳細資料](/connectors/fileconnector/)。 

## <a name="get-help"></a>取得說明

若要提出問題、回答問題以及了解其他 Azure Logic Apps 使用者的做法，請造訪 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

若要改善 Azure Logic Apps 和連接器，請在 [Azure Logic Apps 使用者意見反應網站](http://aka.ms/logicapps-wish)上票選或提交想法。

## <a name="next-steps"></a>後續步驟

- 從邏輯應用程式[連線到內部部署資料](../logic-apps/logic-apps-gateway-connection.md)
- 了解[企業整合](../logic-apps/logic-apps-enterprise-integration-overview.md)

