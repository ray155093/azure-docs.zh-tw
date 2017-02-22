---
title: "在 Azure Logic Apps 中編碼 X12 訊息 | Microsoft Docs"
description: "如何在您的邏輯應用程式中使用企業整合套件內的 X12 編碼器"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: bd339e60aff1edca7f86e0ae82746f55eb67b296
ms.openlocfilehash: 9f7b53cfafa8c14ab46cf80015afc8da4e0fda2b


---
# <a name="get-started-with-encode-x12-message"></a>開始使用編碼 X12 訊息
驗證 EDI 和夥伴特定屬性，將 XML 編碼訊息轉換為交換中的 EDI 交易集，並要求技術和/或功能確認

## <a name="prereqs"></a>必要條件
* Azure 帳戶；您可以建立一個 [免費帳戶](https://azure.microsoft.com/free)
* 需要有整合帳戶才能使用編碼 x12 訊息連接器。 請參閱如何建立[整合帳戶](logic-apps-enterprise-integration-create-integration-account.md)、[合作夥伴](logic-apps-enterprise-integration-partners.md)和 [X12 合約](logic-apps-enterprise-integration-x12.md)的詳細資料

## <a name="encode-x12-messages"></a>編碼 X12 訊息

1. [建立邏輯應用程式](logic-apps-create-a-logic-app.md)。
2. 此連接器並沒有任何觸發程序。 您可以使用其他觸發程序來啟動邏輯應用程式，例如 [要求] 觸發程序。  在邏輯應用程式設計工具中，新增一個觸發程序，然後新增一個動作。  從下拉式清單中選取 [顯示 Microsoft Managed API]，然後在搜尋方塊中輸入 "x12"。  選取 [X12 - 由協議名稱編碼 X12 訊息] 或是 [X12 - 由身分識別編碼 X12 訊息]：  
   
    ![搜尋 x12](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 
3. 如果您之前尚未建立與整合帳戶的任何連線，系統將會提示您輸入連線詳細資料：
   
    ![整合帳戶連線](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png) 
4. 輸入整合帳戶詳細資料。  具有星號的屬性為必要項目：
   
   | 屬性 | 詳細資料 |
   | --- | --- |
   | 連線名稱 * |為連線輸入任何名稱 |
   | 整合帳戶 * |輸入整合帳戶名稱。 請確定您的整合帳戶和邏輯應用程式位於相同的 Azure 位置 |
   
    完成後，連線詳細資料看起來類似下圖︰
   
    ![整合帳戶連線已建立](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png) 
5. 選取 [ **建立**]。
6. 請注意，已建立連線。
   
    ![整合帳戶連線詳細資料](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>由協議名稱編碼 X12 訊息
從下拉式清單選取 X12 協議和 XML 訊息來編碼：     
    ![提供必要欄位](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png) 

#### <a name="encode-x12-messages-by-identities"></a>由身分識別編碼 X12 訊息
輸入傳送者識別項、傳送者辨識符號、接收者識別項和接收者辨識符號，如 X12 協議中所設定。  選取要編碼的 XML 訊息︰
   
   ![提供必要欄位](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>X12 編碼詳細資料

X12 編碼連接器會執行下列動作︰

* 藉由比對傳送者和接收者內容屬性進行合約解析。
* 序列化 EDI 交換，將 XML 編碼訊息轉換為交換中的 EDI 交易集。
* 套用交易集標頭和結尾區段
* 產生交換控制編號、群組控制編號和每個傳出交換的交易集控制編號
* 取代承載資料中的分隔符號
* 驗證 EDI 和夥伴特定屬性
  * 針對訊息結構描述進行交易集資料元素的結構描述驗證
  * 對交易集資料元素執行 EDI 驗證。
  * 對交易集資料元素執行擴充驗證
* 要求技術和/或功能確認 (若已設定)。
  * 標頭驗證後會產生技術確認。 技術確認會報告位址接收者處理交換標頭和結尾的狀態
  * 內文驗證後會產生功能確認。 功能確認會報告處理接收的文件時遇到的每個錯誤

## <a name="next-steps"></a>後續步驟
[深入了解企業整合套件](logic-apps-enterprise-integration-overview.md "了解企業整合套件") 




<!--HONumber=Jan17_HO5-->


