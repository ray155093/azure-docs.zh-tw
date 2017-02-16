---
title: "編碼 Azure Logic Apps 中的 AS2 訊息 | Microsoft Docs"
description: "如何使用 Enterprise Integration Pack 和 Logic Apps 中隨附的 AS2 編碼器"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: a2ca24c1800a01e6a15af35937a654093cf224af
ms.openlocfilehash: 754ec2e829babd2e5ca3e5e5290db950ef5035e7


---
# <a name="get-started-with-encode-as2-message"></a>開始使用編碼 AS2 訊息
連接到「邊碼 AS2 訊息」以建立傳輸訊息時的安全性和可靠性。 它可透過訊息處置通知 (MDN) 提供數位簽章、加密和通知，這也可能導致支援不可否認性。

## <a name="prereqs"></a>必要條件
* Azure 帳戶；您可以建立一個 [免費帳戶](https://azure.microsoft.com/free)
* 需要有整合帳戶才能使用編碼 AS2 訊息連接器。 請參閱如何建立[整合帳戶](logic-apps-enterprise-integration-create-integration-account.md)、[合作夥伴](logic-apps-enterprise-integration-partners.md)和 [AS2 合約](logic-apps-enterprise-integration-as2.md)的詳細資料

## <a name="encode-as2-messages"></a>編碼 AS2 訊息
1. [建立邏輯應用程式](logic-apps-create-a-logic-app.md)。
2. 此連接器並沒有任何觸發程序。 您可以使用其他觸發程序來啟動邏輯應用程式，例如 [要求] 觸發程序。  在邏輯應用程式設計工具中，新增一個觸發程序，然後新增一個動作。  從下拉式清單中選取 [顯示 Microsoft Managed API]，然後在搜尋方塊中輸入 "AS2"。  選取 AS2 - 編碼 AS2 訊息：
   
    ![搜尋 AS2](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)
3. 如果您之前尚未建立與整合帳戶的任何連線，系統將會提示您輸入連線詳細資料：
   
    ![建立整合帳戶連線](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  
4. 輸入整合帳戶詳細資料。  具有星號的屬性為必要項目：
   
   | 屬性 | 詳細資料 |
   | --- | --- |
   | 連線名稱 * |為連線輸入任何名稱 |
   | 整合帳戶 * |輸入整合帳戶名稱。 請確定您的整合帳戶和邏輯應用程式位於相同的 Azure 位置 |
   
      完成後，連線詳細資料看起來類似下圖︰
   
      ![已建立整合連線](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)  
5. 選取 [建立] 。
6. 請注意，已建立連線。  提供 AS2-From、AS2-To 識別碼 (如協議所設定) 和內文 (訊息承載) 詳細資料：
   
    ![提供必要欄位](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>AS2 編碼器詳細資料
編碼 AS2 連接器會執行下列動作︰ 

* 套用 AS2/HTTP 標頭
* 簽署外寄訊息 (若已設定)
* 加密外寄訊息 (若已設定)
* 壓縮訊息 (若已設定)

## <a name="try-it-yourself"></a>親自試試看
歡迎試用。 使用 [AS2 邏輯應用程式範本和案例](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)來部署可完整運作的邏輯應用程式。

## <a name="next-steps"></a>後續步驟
[深入了解企業整合套件](logic-apps-enterprise-integration-overview.md "了解企業整合套件") 




<!--HONumber=Jan17_HO5-->


