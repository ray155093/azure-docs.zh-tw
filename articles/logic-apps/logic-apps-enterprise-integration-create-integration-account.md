---
title: "在 Azure Logic Apps 中建立、連結、刪除或移動整合帳戶 | Microsoft Docs"
description: "如何建立整合帳戶，並將它連結到您的邏輯應用程式"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: dca762854f22721de76f69cbc28c0377d563fe28
ms.openlocfilehash: e26271a1291fc76e3ea8f93a7aa9ab8c811ff604


---

# <a name="what-is-an-integration-account"></a>什麼是整合帳戶？
整合帳戶讓企業整合應用程式能夠管理包括結構描述、對應、憑證、夥伴和協議在內的構件。 您所建立的任何整合應用程式會使用整合帳戶來存取這些結構描述、對應、憑證等。

## <a name="create-an-integration-account"></a>建立整合帳戶
1. 選取 [瀏覽]：   
   ![](./media/logic-apps-enterprise-integration-accounts/account-1.png)  
2. 在篩選搜尋方塊中輸入**整合**，然後從結果清單中選取 [整合帳戶]：     
   ![](./media/logic-apps-enterprise-integration-accounts/account-2.png)  
3. 在頁面頂端的功能表中，選取 [新增] 按鈕：      
   ![](./media/logic-apps-enterprise-integration-accounts/account-3.png)  
4. 輸入 [名稱]、選取您想要使用的 [訂用帳戶]、建立新的 [資源群組] 或選取現有的資源群組、選取將裝載您整合帳戶的 [位置]、選取 [定價層]，然後選取 [建立 ] 按鈕。   
   
   此時，整合帳戶將會佈建於您選取的位置。 這應該可在 1 分鐘內完成。    
   ![](./media/logic-apps-enterprise-integration-accounts/account-4.png)  
5. 重新整理頁面。 您會看到已列出新的整合帳戶：  
   ![](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

接下來，將您剛剛建立的整合帳戶連結至您的邏輯應用程式。 

## <a name="link-an-integration-account-to-a-logic-app"></a>將整合帳戶連結至邏輯應用程式
如果要使您的邏輯應用程式存取整合帳戶中的對應、結構描述、協議與其他構件，請將整合帳戶連結到邏輯應用程式。

#### <a name="prereqs"></a>必要條件
* 整合帳戶
* 邏輯應用程式

> [!NOTE] 
> 開始之前，請確定您的整合帳戶和邏輯應用程式位於**相同的 Azure 位置**。

1. 從邏輯應用程式的功能表中選取 [設定] 連結：  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)   
2. 從 [設定] 刀鋒視窗中選取 [整合帳戶] 項目：  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)   
3. 從 [選取整合帳戶] 下拉式清單方塊中，選取您想要連結到邏輯應用程式的整合帳戶：  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)   
4. 儲存您的工作：  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-4.png)   
5. 您將會看到一則通知，表示您的整合帳戶已經連結到邏輯應用程式，而整合帳戶中的所有構件現在都可在邏輯應用程式中使用：  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)   

現在您的整合帳戶已連結至邏輯應用程式，您便可以在邏輯應用程式內使用 B2B 連接器。 部分常見的 B2B 連接器包括 XML 驗證、一般檔案編碼/解碼。  

## <a name="how-to-delete-an-integration-account"></a>如何刪除整合帳戶？
1. 選取 [瀏覽]：  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. 在篩選搜尋方塊中輸入**整合**，然後從結果清單中選取 [整合帳戶]：     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. 選取您想要刪除的**整合帳戶**：  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. 選取位於功能表上的 [刪除] 連結：   
   ![](./media/logic-apps-enterprise-integration-accounts/delete.png)  
5. 確認您的選擇。    

## <a name="how-to-move-an-integration-account"></a>如何移動整合帳戶？
您可以將整合帳戶輕鬆地移到新訂用帳戶和新的資源群組。 如果您需要移動整合帳戶，請遵循下列步驟：

> [!IMPORTANT]
> 您需要在移動整合帳戶之後更新所有指令碼，以使用新的資源識別碼。

1. 選取 [瀏覽]：  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. 在篩選搜尋方塊中輸入**整合**，然後從結果清單中選取 [整合帳戶]：     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. 選取您想要刪除的**整合帳戶**：  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. 選取位於功能表上的 [移動] 連結：   
   ![](./media/logic-apps-enterprise-integration-accounts/move.png)  
5. 確認您的選擇。    

## <a name="next-steps"></a>後續步驟
* [深入了解合約](../logic-apps/logic-apps-enterprise-integration-agreements.md "了解企業整合合約")  




<!--HONumber=Jan17_HO5-->


