---
title: 了解企業整合套件編碼 AS2 訊息連接器 | Microsoft Docs
description: 了解如何使用夥伴搭配企業整合套件與 Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: erikre
editor: ''

ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: padmavc

---
# 開始使用編碼 AS2 訊息
連接到「邊碼 AS2 訊息」以建立傳輸訊息時的安全性和可靠性。它可透過訊息處置通知 (MDN) 提供數位簽章、加密和通知，這也可能導致支援不可否認性。

## 建立連線
### 必要條件
* Azure 帳戶；您可以建立一個[免費帳戶](https://azure.microsoft.com/free)
* 需要有整合帳戶才能使用編碼 AS2 訊息連接器。詳細資料請參閱如何建立[整合帳戶](app-service-logic-enterprise-integration-create-integration-account.md)、[合作夥伴](app-service-logic-enterprise-integration-partners.md)和 [AS2 合約](app-service-logic-enterprise-integration-as2.md)

### 使用下列步驟連線至編碼 AS2 訊息︰
1. [建立邏輯應用程式](app-service-logic-create-a-logic-app.md)可提供範例
2. 此連接器並沒有任何觸發程序。您可以使用其他觸發程序來啟動邏輯應用程式，例如 [要求] 觸發程序。在邏輯應用程式設計工具中，新增一個觸發程序，然後新增一個動作。從下拉式清單中選取 [顯示 Microsoft Managed API]，然後在搜尋方塊中輸入 "AS2"。選取 AS2 - 編碼 AS2 訊息
   
    ![搜尋 AS2](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage1.png)
3. 如果您之前尚未建立與整合帳戶的任何連線，系統將會提示您輸入連線詳細資料
   
    ![建立整合帳戶連線](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage1.png)
4. 輸入整合帳戶詳細資料。具有星號的屬性為必要項目
   
   | 屬性 | 詳細資料 |
   | --- | --- |
   | 連線名稱 * |為連線輸入任何名稱 |
   | 整合帳戶 * |輸入整合帳戶名稱。請確定您的整合帳戶和邏輯應用程式位於相同的 Azure 位置 |
   
      完成後，連線詳細資料看起來類似下圖
   
      ![已建立整合連線](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage2.png)
5. 選取 [建立]
6. 請注意，已建立連線。提供 AS2-From、AS2-To 識別碼 (如合約所設定) 和內文 (訊息承載) 詳細資料。
   
    ![提供必要欄位](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage3.png)

## AS2 編碼會執行下列動作：
* 套用 AS2/HTTP 標頭
* 簽署外寄訊息 (若已設定)
* 加密外寄訊息 (若已設定)
* 壓縮訊息 (若已設定)

## 親身體驗
何不嘗試看看。按一下[這裡](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)，使用 Logic Apps AS2 功能部署您自己的全功能邏輯應用程式

## 後續步驟
[深入了解企業整合套件](app-service-logic-enterprise-integration-overview.md "了解企業整合套件")

<!---HONumber=AcomDC_0914_2016-->