---
title: "了解企業整合套件解碼 AS2 訊息連接器 | Microsoft Docs"
description: "了解如何使用夥伴搭配企業整合套件與 Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: erikre
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5836e0d2bb7d1d97e9f175396f51047af0f84ca5


---
# <a name="get-started-with-decode-as2-message"></a>開始使用解碼 AS2 訊息
連接到「解碼 AS2 訊息」以建立傳輸訊息時的安全性和可靠性。 它可透過訊息處置通知 (MDN) 提供數位簽章、解密和通知。

## <a name="create-the-connection"></a>建立連線
### <a name="prerequisites"></a>必要條件
* Azure 帳戶；您可以建立一個 [免費帳戶](https://azure.microsoft.com/free)
* 需要有整合帳戶才能使用解碼 AS2 訊息連接器。 請參閱如何建立[整合帳戶](app-service-logic-enterprise-integration-create-integration-account.md)、[合作夥伴](app-service-logic-enterprise-integration-partners.md)和 [AS2 合約](app-service-logic-enterprise-integration-as2.md)的詳細資料

### <a name="connect-to-decode-as2-message-using-the-following-steps"></a>使用下列步驟連線至解碼 AS2 訊息︰
1. [建立邏輯應用程式](app-service-logic-create-a-logic-app.md)可提供範例。
2. 此連接器並沒有任何觸發程序。 您可以使用其他觸發程序來啟動邏輯應用程式，例如 [要求] 觸發程序。  在邏輯應用程式設計工具中，新增一個觸發程序，然後新增一個動作。  從下拉式清單中選取 [顯示 Microsoft Managed API]，然後在搜尋方塊中輸入 "AS2"。  選取 AS2 - 解碼 AS2 訊息
   
    ![搜尋 AS2](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage1.png)
3. 如果您之前尚未建立與整合帳戶的任何連線，系統將會提示您輸入連線詳細資料
   
    ![建立整合連線](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage2.png)
4. 輸入整合帳戶詳細資料。  具有星號的屬性為必要項目
   
   | 屬性 | 詳細資料 |
   | --- | --- |
   | 連線名稱 * |為連線輸入任何名稱 |
   | 整合帳戶 * |輸入整合帳戶名稱。 請確定您的整合帳戶和邏輯應用程式位於相同的 Azure 位置 |
   
      完成後，連線詳細資料看起來類似下圖
   
      ![整合連線](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage3.png)
5. 選取 [建立] 
6. 請注意，已建立連線。  現在，在您的邏輯應用程式中繼續其他步驟
   
    ![已建立整合連線](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage4.png) 
7. 從要求輸出選取內文和標頭
   
    ![提供必要欄位](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage5.png) 

## <a name="the-as2-decode-does-the-following"></a>AS2 解碼會執行下列動作：
* 處理 AS2/HTTP 標頭
* 驗證簽章 (若已設定)
* 將訊息解密 (若已設定)
* 將訊息解壓縮 (若已設定)
* 協調收到的 MDN 與原始輸出訊息
* 更新不可否認性資料庫中的記錄並使其相互關聯
* 寫入記錄以便進行 AS2 狀態報告
* 輸出承載內容是以 base64 編碼
* 決定是否需要 MDN，以及根據 AS2 合約中的組態決定 MDN 應為同步或非同步
* 產生同步或非同步 MDN (根據合約組態)
* 在 MDN 上設定相互關聯權杖和屬性

## <a name="try-it-for-yourself"></a>親身體驗
何不嘗試看看。 按一下 [這裡](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) ，使用 Logic Apps AS2 功能部署您自己的全功能邏輯應用程式 

## <a name="next-steps"></a>後續步驟
[深入了解企業整合套件](app-service-logic-enterprise-integration-overview.md "了解企業整合套件") 




<!--HONumber=Nov16_HO3-->


