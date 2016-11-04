---
title: 企業整合套件的對應概觀 | Microsoft Docs
description: 了解如何使用對應搭配企業整合套件與 Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun

ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe

---
# 了解對應與企業整合套件
## Overview
企業整合會使用對應，將 XML 資料從某種格式轉換成其他格式。

## 何謂對應？
對應是一份 XML 文件，可定義應將文件中的哪些資料轉換成其他格式。

## 為什麼要使用對應？
讓我們假設您會定期收到客戶的 B2B 訂單或發票，而該客戶使用 YYYMMDD 格式的日期。不過，在您的組織中，您是以 MMDDYYY 格式儲存日期。您可以使用對應，先將 YYYMMDD 日期格式「轉換」為 MMDDYYY，然後再將訂單或發票儲存於客戶活動資料庫中。

## 如何建立對應？
Visual Studio 2015 的 [Enterprise 整合套件](app-service-logic-enterprise-integration-overview.md "了解企業整合套件")可用來建立 Biztalk 整合專案。建立整合對應檔案可讓您以視覺化方式對應兩個 XML 結構描述檔案之間的項目。在建置此專案之後，XSLT 文件是輸出。

## 如何上傳對應？
從 Azure 入口網站：

1. 選取 [瀏覽] ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. 在篩選搜尋方塊中輸入**整合**，然後從結果清單中選取 [整合帳戶] ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. 選取您將新增對應的**整合帳戶** ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4. 選取 [對應] 圖格 ![](./media/app-service-logic-enterprise-integration-maps/map-1.png)
5. 在開啟的 [對應] 刀鋒視窗中選取 [新增] 按鈕 ![](./media/app-service-logic-enterprise-integration-maps/map-2.png)
6. 輸入對應的**名稱**，接著，若要上傳對應檔案，可選取 [對應] 文字方塊右邊的資料夾圖示。完成上傳程序之後，選取 [確定] 按鈕。![](./media/app-service-logic-enterprise-integration-maps/map-3.png)
7. 現在已將對應新增到您的整合帳戶中。您將會收到螢幕上的通知，指出新增對應檔案成功或失敗。當您收到通知之後，選取 [對應] 圖格，您接著將會在 [對應] 刀鋒視窗中看到新加入的對應︰![](./media/app-service-logic-enterprise-integration-maps/map-4.png)

## 如何編輯對應？
若要編輯對應，您必須上傳新的對應檔，其中含有您所需的變更。您可以先下載對應並加以編輯。

遵循下列步驟來上傳新的對應，以取代現有的對應：

1. 選取 [對應] 圖格
2. 開啟 [對應] 刀鋒視窗時，選取您想要編輯的對應
3. 在 [對應] 刀鋒視窗中，選取 [更新] 連結 ![](./media/app-service-logic-enterprise-integration-maps/edit-1.png)
4. 選取您想要上傳的對應檔案，方法是使用開啟的檔案選擇器對話方塊，然後在檔案選擇器中選取 [開啟] ![](./media/app-service-logic-enterprise-integration-maps/edit-2.png)
5. 上傳對應之後，您將會收到通知快顯視窗。

## 如何刪除對應？
1. 選取 [對應] 圖格
2. 開啟 [對應] 刀鋒視窗時，選取您想要刪除的對應
3. 選取 [刪除] 連結 ![](./media/app-service-logic-enterprise-integration-maps/delete.png)
4. 確認您真的想要刪除對應。![](./media/app-service-logic-enterprise-integration-maps/delete-confirmation-1.png)

## 後續步驟
* [深入了解企業整合套件](app-service-logic-enterprise-integration-overview.md "了解企業整合套件")
* [深入了解合約](app-service-logic-enterprise-integration-agreements.md "了解企業整合合約")
* [深入了解轉換](app-service-logic-enterprise-integration-transform.md "了解企業整合轉換")

<!---HONumber=AcomDC_0810_2016------>