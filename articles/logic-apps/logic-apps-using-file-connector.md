---
title: "連接至 Azure Logic Apps 中的內部部署檔案系統資料夾 | Microsoft Docs"
description: "使用內部部署資料閘道連接至邏輯應用程式工作流程中的內部部署檔案系統"
services: logic-apps
documentationcenter: dev-center-name
author: derek1ee
manager: anneta
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2017
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: 336e3f2bba2fe3bb7abdd6959354349ff61ed6a8
ms.openlocfilehash: 3bbcb5e9c7a4731eb8eb410a94fddb93b2ea8416


---
# <a name="use-the-file-system-connector-with-the-on-premises-data-gateway-in-a-logic-app"></a>在邏輯應用程式中使用檔案系統連接器搭配內部部署資料閘道

混合式雲端連線是 Logic Apps 的核心。 內部部署資料閘道可讓您管理資料，並且從 Logic Apps 安全地存取內部部署的資源。 在本文中，我們會以一個簡單的案例來示範如何連接到內部部署檔案系統︰將已上傳至 Dropbox 的檔案複製到檔案共用，然後傳送電子郵件。

## <a name="prerequisites"></a>必要條件
- 安裝及設定最新的[內部部署資料閘道](https://www.microsoft.com/en-us/download/details.aspx?id=53127)。
- 安裝版本 1.15.6150.1 或更高版本的最新內部部署資料閘道。 [連接至內部部署資料閘道](http://aka.ms/logicapps-gateway)會列出步驟。 必須先在內部部署機器上安裝閘道，才能繼續執行其餘的步驟。

## <a name="use-file-system-connector"></a>使用檔案系統連接器

1. 讓我們建立 Dropbox「建立檔案時」觸發程序，則速覽所有支援的檔案連接器動作就如同在搜尋中輸入「檔案系統」一樣簡單。

 ![搜尋檔案連接器](media/logic-apps-using-file-connector/search-file-connector.png)

2. 選擇 [建立檔案] 並為它建立連線。
 - 如果您目前沒有連線，系統會提示您建立連線。
 - 核取 [透過內部部署資料閘道連線] 選項。 會顯示更多屬性。
 - 選取根資料夾。 根資料夾可以是安裝內部部署資料閘道所在電腦的本機資料夾，或者是電腦可存取的網路共用。
 - 輸入閘道的使用者名稱和密碼。
 - 選取您從上一個步驟安裝的閘道。
    
 > [!NOTE]
 > 根資料夾是主要的父資料夾，會做為所有檔案相關動作的相對路徑。

 ![設定連線](media/logic-apps-using-file-connector/create-file.png)

3. 提供所有詳細資料後，請按一下 [建立]。 Logic Apps 會設定並測試連線，以確定它運作正常。 如果簽出所有項目，您會看到先前所選取卡片的選項。 檔案系統連接器現在已可供使用。

4. 取得已上傳至 Dropbox 的檔案，並將它複製到內部部署上檔案共用的根資料夾。

 ![建立檔案動作](media/logic-apps-using-file-connector/create-file-filled.png)

5. 複製檔案後，我們會傳送電子郵件，讓相關使用者得知。 如同其他連接器一樣，先前動作的輸出可在「動態內容」選取器取得。
 - 輸入電子郵件的收件者、標題和本文。 使用「動態內容」選取器來挑選檔案連接器的輸出，讓電子郵件內容加豐富。

 ![傳送電子郵件動作](media/logic-apps-using-file-connector/send-email.png)

6. 儲存您的邏輯應用程式，並藉由將檔案上傳至 Dropbox 加以測試。 您應看見檔案正複製到內部部署檔案共用，並接收此作業的電子郵件通知。

    > [!TIP] 
    > 請查看如何[監視 Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md)。

7. 全部完成，您現在有使用檔案系統連接器的運作中邏輯應用程式。 您可以開始探索它所提供的其他功能︰

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

## <a name="next-steps"></a>後續步驟
- 了解[企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md)。 
- 建立 Logic Apps 的[內部部署連線](../logic-apps/logic-apps-gateway-connection.md)。



<!--HONumber=Jan17_HO5-->


