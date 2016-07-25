<properties 
	pageTitle="結構描述與企業整合套件的概觀 | Microsoft Azure App Service | Microsoft Azure" 
	description="了解如何使用結構描述搭配企業整合套件與 Logic Apps" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# 了解結構描述與企業整合套件  

## 為什麼要使用結構描述
您可以使用結構描述來確認您收到的 XML 文件都有效，這表示文件會以預先定義格式包含預期的資料。

## 如何新增結構描述
從 Azure 入口網站：
1. 選取 [瀏覽] ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. 在篩選搜尋方塊中輸入**整合**，然後從結果清單中選取 [整合帳戶] ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. 選取您將新增結構描述的**整合帳戶** ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  選取 [結構描述] 圖格 ![](./media/app-service-logic-enterprise-integration-schemas/schema-1.png)
5. 在開啟的 [結構描述] 刀鋒視窗中選取 [新增] 按鈕 ![](./media/app-service-logic-enterprise-integration-schemas/schema-2.png)
6. 輸入結構描述的**名稱**，接著，若要上傳結構描述檔案，可選取 [結構描述] 文字方塊右邊的資料夾圖示。完成上傳程序之後，選取 [確定] 按鈕。![](./media/app-service-logic-enterprise-integration-schemas/schema-3.png)
7. 選取「鈴鐺」通知圖示，以查看結構描述上傳程序的進度。![](./media/app-service-logic-enterprise-integration-schemas/schema-4.png)
8. 選取 [結構描述] 圖格。這會重新整理該圖格，而您應該會看到結構描述數目增加，反映已成功加入新的結構描述。選取 [結構描述] 圖格之後，您也會看見新加入的夥伴顯示於 [結構描述] 刀鋒視窗的右邊。![](./media/app-service-logic-enterprise-integration-schemas/schema-5.png)


## 如何使用結構描述
- 結構描述可用來驗證在 B2B 案例中交換的訊息。

## 如何編輯結構描述
1. 選取 [結構描述] 圖格
2. 從開啟的 [結構描述] 刀鋒視窗中選取您想要編輯的結構描述
3. 選取 [結構描述] 刀鋒視窗中的 [上傳] 連結 ![](./media/app-service-logic-enterprise-integration-schemas/edit-1.png)
4. 使用開啟的檔案選擇器對話方塊，選取您想要上傳的結構描述檔案。
5. 在檔案選擇器中選取 [開啟] ![](./media/app-service-logic-enterprise-integration-schemas/edit-2.png)
6. 您將會收到通知，指出上傳成功 ![](./media/app-service-logic-enterprise-integration-schemas/edit-3.png)

## 如何刪除結構描述
1. 選取 [結構描述] 圖格
2. 從開啟的 [結構描述] 刀鋒視窗中，選取您想要刪除的結構描述
3. 從 [結構描述] 刀鋒視窗功能表列中選取 [刪除] 連結 ![](./media/app-service-logic-enterprise-integration-schemas/delete-1.png)
4. 如果您真的想要刪除所選取的結構描述，選擇 [刪除結構描述] 對話方塊上的 [是] 來確認您的選擇 ![](./media/app-service-logic-enterprise-integration-schemas/delete-2.png)
5. 最後，請注意，[結構描述] 刀鋒視窗中的結構描述清單重會新整理，而且不會再列出您已刪除的結構描述 ![](./media/app-service-logic-enterprise-integration-schemas/delete-3.png)

## 後續步驟

- [深入了解企業整合套件](./app-service-logic-enterprise-integration-overview.md "了解企業整合套件")

      

<!---HONumber=AcomDC_0713_2016-->