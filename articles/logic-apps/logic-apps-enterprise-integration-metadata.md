---
title: "Azure Logic Apps 整合帳戶中繼資料 |Microsoft Docs"
description: "整合帳戶中繼資料概觀"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: e5d567800d00b41ec0782216b442f437a2500928
ms.openlocfilehash: 41edd713d85790793341e100f77300f999ac8c73


---
# <a name="azure-logic-apps-integration-account-metadata"></a>Azure Logic Apps 整合帳戶中繼資料

## <a name="overview"></a>概觀

合作夥伴、協議、結構描述、對應已新增至整合帳戶，並以索引鍵-值組儲存中繼資料。 您可以定義可在執行階段期間擷取的自訂中繼資料。  構件現在無法在 UI 中建立中繼資料；您可以使用 Rest API 來建立它們。  合作夥伴、協議和結構描述有 [編輯為 JSON] 並允許輸入中繼資料資訊。  在邏輯應用程式中，[整合帳戶構件查閱] 有助於擷取中繼資料資訊。

## <a name="how-to-store-metadata"></a>如何儲存中繼資料

1. 建立[整合帳戶](logic-apps-enterprise-integration-create-integration-account.md)   

2. 將[合作夥伴](logic-apps-enterprise-integration-partners.md#how-to-create-a-partner)或[協議](logic-apps-enterprise-integration-agreements.md#how-to-create-agreements)或[結構描述](logic-apps-enterprise-integration-schemas.md)新增至整合帳戶

3. 選取合作夥伴或協議，或結構描述。 選取 [編輯為 JSON] 並輸入中繼資料詳細資訊    
![輸入中繼資料](media/logic-apps-enterprise-integration-metadata/image1.png)  

## <a name="call-integration-account-artifact-lookup-from-a-logic-app"></a>從邏輯應用程式呼叫**整合帳戶構件查閱**

1. 建立[邏輯應用程式](logic-apps-create-a-logic-app.md)

2. [連結](logic-apps-enterprise-integration-create-integration-account.md#link-an-integration-account-to-a-logic-app)邏輯應用程式與整合帳戶    

3. 在搜尋 [整合帳戶構件查閱] 之前，建立觸發程序，例如使用「要求」或 HTTP。  搜尋**整合**以尋找 [整合帳戶構件查閱]  
![搜尋查閱](media/logic-apps-enterprise-integration-metadata/image2.png)

3. 選取 [整合帳戶構件查閱]  

4. 選取 [構件類型] 並提供 [構件名稱]  
![搜尋查閱](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="an-example-to-retrieve-partner-metadata"></a>擷取合作夥伴中繼資料的範例

1. 合作夥伴中繼資料具有路由 url 詳細資料    
![搜尋查閱](media/logic-apps-enterprise-integration-metadata/image6.png)

2. 在邏輯應用程式中設定 [整合帳戶構件查閱] 和 [HTTP]   
![搜尋查閱](media/logic-apps-enterprise-integration-metadata/image4.png)

3. 若要擷取 URI，程式碼檢視應該如下所示：    
![搜尋查閱](media/logic-apps-enterprise-integration-metadata/image5.png)


## <a name="next-steps"></a>後續步驟
* [深入了解合約](logic-apps-enterprise-integration-agreements.md "了解企業整合合約")  



<!--HONumber=Feb17_HO1-->


