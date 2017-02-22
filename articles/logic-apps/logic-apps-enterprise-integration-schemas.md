---
title: "結構描述與企業整合套件的概觀 | Microsoft Docs"
description: "了解如何使用結構描述搭配企業整合套件與邏輯應用程式"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 2ad6ad4dcab9a7296dfc6fb6227c510daee85222


---
# <a name="learn-about-schemas-and-the-enterprise-integration-pack"></a>了解結構描述與企業整合套件
## <a name="why-use-a-schema"></a>為什麼要使用結構描述？
您可以使用結構描述來確認您收到的 XML 文件都有效，預期的資料會使用預先定義的格式。 結構描述可用來驗證在 B2B 案例中交換的訊息。

## <a name="add-a-schema"></a>新增結構描述
從 Azure 入口網站：  

1. 選取 [更多服務]。  
   ![Azure 入口網站的螢幕擷取畫面，反白顯示了 [更多服務]](media/logic-apps-enterprise-integration-schemas/overview-11.png)    
2. 在篩選搜尋方塊中輸入**整合**，然後從結果清單中選取 [整合帳戶]。     
   ![篩選搜尋方塊的螢幕擷取畫面](media/logic-apps-enterprise-integration-schemas/overview-21.png)  
3. 選取您要新增結構描述的**整合帳戶**。    
   ![整合帳戶清單的螢幕擷取畫面](media/logic-apps-enterprise-integration-schemas/overview-31.png)  
4. 選取 [結構描述] 圖格。  
   ![IEP 整合帳戶的螢幕擷取畫面，反白顯示了 [結構描述]](media/logic-apps-enterprise-integration-schemas/schema-11.png)  

### <a name="add-a-schema-file-less-than-2-mb"></a>新增少於 2 MB 的結構描述檔案
1. 在開啟的 [結構描述] 刀鋒視窗中 (從先前的步驟)，選取 [新增]。  
   ![結構描述刀鋒視窗的螢幕擷取畫面，反白顯示了 [新增] 按鈕](media/logic-apps-enterprise-integration-schemas/schema-21.png)  
2. 輸入結構描述名稱。 接著，若要上傳結構描述檔案，可選取 [結構描述] 文字方塊旁的資料夾圖示。 完成上傳程序之後，選取 [確定]。    
   ![[新增結構描述] 的螢幕擷取畫面，反白顯示了 [小型檔案]](media/logic-apps-enterprise-integration-schemas/schema-31.png)  

### <a name="add-a-schema-file-larger-than-2-mb-up-to-a-maximum-of-8-mb"></a>新增大於 2 MB 的結構描述檔案 (最大 8 MB)
這個程序取決於 blob 容器的存取層級：**公用**或**無匿名存取**。 若要決定此存取層級，請在 [Azure 儲存體總管] 中，於 [Blob 容器] 下，選取您想要的 blob 容器。 選取 [安全性]，然後選取 [存取層級] 索引標籤。

1. 如果 blob 安全性存取層級為**公用**，請遵循下列步驟。  
   ![Azure 儲存體總管的螢幕擷取畫面，反白顯示了 [Blob 容器]、[安全性] 和 [公用]](media/logic-apps-enterprise-integration-schemas/blob-public.png)  
   
    a. 將結構描述上傳至儲存體，並複製 URI。  
    ![儲存體帳戶的螢幕擷取畫面，反白顯示了 URI](media/logic-apps-enterprise-integration-schemas/schema-blob.png)  
   
    b. 在 [新增結構描述] 中選取 [大型檔案]，然後在 [內容 URI] 文字方塊中提供 URI。  
    ![結構描述刀鋒視窗的螢幕擷取畫面，反白顯示了 [新增] 按鈕和 [大型檔案]](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)  
2. 如果 blob 安全性存取層級為**無匿名存取**，請遵循下列步驟。  
   ![Azure 儲存體總管的螢幕擷取畫面，反白顯示了 [Blob 容器]、[安全性] 和 [無匿名存取]](media/logic-apps-enterprise-integration-schemas/blob-1.png)  
   
    a. 將結構描述上傳至儲存體。  
    ![儲存體帳戶的螢幕擷取畫面](media/logic-apps-enterprise-integration-schemas/blob-3.png)
   
    b. 產生結構描述的共用存取簽章。  
    ![儲存體帳戶的螢幕擷取畫面，反白顯示了 [共用存取簽章] 索引標籤](media/logic-apps-enterprise-integration-schemas/blob-2.png)
   
    c. 在 [新增結構描述] 中選取 [大型檔案]，然後在 [內容 URI] 文字方塊中提供共用存取簽章 URI。  
    ![結構描述刀鋒視窗的螢幕擷取畫面，反白顯示了 [新增] 按鈕和 [大型檔案]](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)  
3. 在 EIP 整合帳戶的 [結構描述] 刀鋒視窗中，您現在應該會看到新增的結構描述。  
   ![EIP 整合帳戶的螢幕擷取畫面，反白顯示了 [結構描述] 和新的結構描述](media/logic-apps-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>編輯結構描述
1. 選取 [結構描述] 圖格。  
2. 從開啟的 [結構描述] 刀鋒視窗中選取您想要編輯的結構描述。
3. 在 [結構描述] 刀鋒視窗中，選取 [編輯]。  
   ![結構描述刀鋒視窗的螢幕擷取畫面](media/logic-apps-enterprise-integration-schemas/edit-12.png)    
4. 使用開啟的檔案選擇器對話方塊，選取您想要編輯的結構描述檔案。
5. 在檔案選擇器中選取 [開啟]。  
   ![檔案選擇器的螢幕擷取畫面](media/logic-apps-enterprise-integration-schemas/edit-31.png)  
6. 您會收到通知指出上傳成功。  

## <a name="delete-schemas"></a>刪除結構描述
1. 選取 [結構描述] 圖格。  
2. 從開啟的 [結構描述] 刀鋒視窗中，選取您想要刪除的結構描述。  
3. 在 [結構描述] 刀鋒視窗中，選取 [刪除]。
   ![結構描述刀鋒視窗的螢幕擷取畫面](media/logic-apps-enterprise-integration-schemas/delete-12.png)  
4. 若要確認您的選擇，請選取 [是]。  
   ![[刪除結構描述] 確認訊息的螢幕擷取畫面](media/logic-apps-enterprise-integration-schemas/delete-21.png)  
5. 最後，請注意，[結構描述] 刀鋒視窗中的結構描述清單重會新整理，而且不會再列出您已刪除的結構描述。  
   ![EIP 整合帳戶的螢幕擷取畫面，反白顯示了 [結構描述]](media/logic-apps-enterprise-integration-schemas/delete-31.png)    

## <a name="next-steps"></a>後續步驟
* [深入了解企業整合套件](logic-apps-enterprise-integration-overview.md "了解企業整合套件")。  




<!--HONumber=Jan17_HO3-->


