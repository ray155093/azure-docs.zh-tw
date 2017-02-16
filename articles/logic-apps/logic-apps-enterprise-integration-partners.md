---
title: "了解夥伴與企業整合套件 | Microsoft Docs"
description: "了解如何使用夥伴搭配企業整合套件與 Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 549dd716df6ee64892ef33ffe4d42751d8662ccb


---
# <a name="learn-about-partners-and-enterprise-integration-pack"></a>了解夥伴與企業整合套件
## <a name="overview"></a>概觀
在您可以建立夥伴之前，您和您想要進行交易的組織必須能夠共用資訊，以協助您識別及驗證彼此所傳送的訊息。 經過這些討論，而且您已準備好開始商務關係之後，就可以在整合帳戶中建立「夥伴」  。

## <a name="what-is-a-partner"></a>什麼是夥伴？
夥伴是參與企業對企業 (B2B) 傳訊和交易的實體。 

## <a name="how-are-partners-used"></a>如何使用夥伴？
夥伴可用來建立合約。 合約會定義將在夥伴間交換之訊息的詳細資訊。 

在您可以建立合約之前，至少需要在整合帳戶中新增兩個以上的夥伴。 在合約內，其中一個夥伴必須是您的組織。 代表您組織的夥伴稱為 **主控夥伴**。 第二個夥伴則代表另一個要與您組織交換訊息的組織。 第二個夥伴稱為 **來賓夥伴**。 來賓夥伴可以是另一家公司，或甚至是您自己組織內的部門。  

新增夥伴之後，您就能使用這些夥伴來建立合約。 

接收和傳送設定是主控夥伴觀點導向的。 例如，合約中的接收設定會決定主控夥伴如何接收來賓夥伴所傳送的訊息。 同樣地，合約中的傳送設定會指出主控夥伴如何將訊息傳送給來賓夥伴。

## <a name="how-to-create-a-partner"></a>如何建立夥伴？
從 Azure 入口網站：  

1. 選取 [瀏覽]  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. 在篩選搜尋方塊中輸入**整合**，然後從結果清單中選取 [整合帳戶]     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. 選取您將在其中新增夥伴的**整合帳戶**  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. 選取 [夥伴]  圖格  
   ![](./media/logic-apps-enterprise-integration-partners/partner-1.png)  
5. 在開啟的 [夥伴] 刀鋒視窗中選取 [新增] 按鈕  
   ![](./media/logic-apps-enterprise-integration-partners/partner-2.png)  
6. 輸入夥伴的 [名稱]，然後選取 [限定詞]，並輸入 [值]。 此值可用來協助識別要傳送到您應用程式的文件。  
   ![](./media/logic-apps-enterprise-integration-partners/partner-3.png)  
7. 選取「鈴鐺」  通知圖示，以查看夥伴建立程序的進度。  
   ![](./media/logic-apps-enterprise-integration-partners/partner-4.png)  
8. 選取 [夥伴]  圖格。 這會重新整理該圖格，而您應該會看到夥伴數目增加，反映已成功加入新的夥伴。    
   ![](./media/logic-apps-enterprise-integration-partners/partner-5.png)  
9. 選取 [夥伴] 圖格之後，您也會看見新加入的夥伴顯示於 [夥伴] 刀鋒視窗中。    
   ![](./media/logic-apps-enterprise-integration-partners/partner-6.png)  

## <a name="how-to-edit-a-partner"></a>如何編輯夥伴
請遵循下列步驟來編輯已經存在於整合帳戶的夥伴：  

1. 選取 [夥伴]  圖格  
2. 開啟 [夥伴] 刀鋒視窗時，選取您想要編輯的夥伴  
3. 在 [更新夥伴]  圖格上，進行您所需的變更  
4. 當您滿意變更時，選取 [儲存] 連結，否則，請選取 [捨棄] 連結以捨棄所做的變更。  
   ![](./media/logic-apps-enterprise-integration-partners/edit-1.png)  

## <a name="how-to-delete-a-partner"></a>如何刪除夥伴
1. 選取 [夥伴]  圖格  
2. 開啟 [夥伴] 刀鋒視窗時，選取您想要編輯的夥伴  
3. 選取 [刪除] 連結    
   ![](./media/logic-apps-enterprise-integration-partners/delete-1.png)   

## <a name="next-steps"></a>後續步驟
* [深入了解合約](../logic-apps/logic-apps-enterprise-integration-agreements.md "了解企業整合合約")  




<!--HONumber=Jan17_HO3-->


