<properties 
	pageTitle="整合帳戶與企業整合套件的概觀 | Microsoft Azure App Service | Microsoft Azure" 
	description="了解關於整合帳戶、企業整合套件與邏輯應用程式的一切" 
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

# 整合帳戶概觀

## 什麼是整合帳戶？
整合帳戶是一個 Azure 帳戶，讓企業整合應用程式能夠管理包括結構描述、對應、憑證、夥伴和合約在內的構件。舉例來說，您所建立的任何整合應用程式都必須使用整合帳戶，才能存取結構描述、對應或憑證。

## 建立整合帳戶 
1. 選取 [瀏覽] ![](./media/app-service-logic-enterprise-integration-accounts/account-1.png)
2. 在篩選搜尋方塊中輸入**整合**，然後從結果清單中選取 [整合帳戶] ![](./media/app-service-logic-enterprise-integration-accounts/account-2.png)
3. 在頁面頂端的功能表中，選取 [新增] 按鈕 ![](./media/app-service-logic-enterprise-integration-accounts/account-3.png)
4. 輸入**名稱**、選取您想要使用的**訂用帳戶**、建立新的**資源群組**或選取現有的資源群組、選取將裝載您整合帳戶的**位置**、選取**定價層**，然後選取 [建立] 按鈕。

  此時，整合帳戶將會佈建於您選取的位置。這應該可在 1 分鐘內完成。![](./media/app-service-logic-enterprise-integration-accounts/account-4.png)
5. 重新整理頁面。您將會看到已列出新的整合帳戶。恭喜！![](./media/app-service-logic-enterprise-integration-accounts/account-5.png)

## 如何將整合帳戶連結到邏輯應用程式
為了讓邏輯應用程式能夠存取對應、結構描述、合約，以及其他位於整合帳戶中的構件，您首先必須將整合帳戶連結到邏輯應用程式。

### 以下是將整合帳戶連結到邏輯應用程式的步驟 

#### 必要條件
- 整合帳戶
- 邏輯應用程式

>[AZURE.NOTE]開始之前，請確定您的整合帳戶和邏輯應用程式位於**相同的 Azure 位置**

1. 從邏輯應用程式的功能表中選取 [設定] 連結 ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-1.png)
2. 從 [設定] 刀鋒視窗中選取 [整合帳戶] 項目 ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-2.png)
3. 從 [選取整合帳戶] 下拉式清單方塊中，選取您想要連結到邏輯應用程式的整合帳戶 ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-3.png)
4. 儲存您的工作 ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-4.png)
5. 您將會看到一則通知，表示您的整合帳戶已經連結到邏輯應用程式，而整合帳戶中的所有構件現在都可在邏輯應用程式中使用。![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-5.png)

現在，已將您的整合帳戶連結到邏輯應用程式，您可以移至邏輯應用程式，並使用 B2B 連接器 (例如 XML 驗證、一般檔案編碼/解碼或轉換) 來建立具備 B2B 功能的應用程式。
    
## 如何刪除整合帳戶？
1. 選取 [瀏覽] ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. 在篩選搜尋方塊中輸入**整合**，然後從結果清單中選取 [整合帳戶] ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. 選取您想要刪除的**整合帳戶** ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4. 選取位於功能表上的 [刪除] 連結 ![](./media/app-service-logic-enterprise-integration-accounts/delete.png)
5. 確認您的選擇

## 如何移動整合帳戶？
您可以將整合帳戶輕鬆地移到新訂用帳戶和新的資源群組。如果您需要移動整合帳戶，請遵循下列步驟：

>[AZURE.IMPORTANT] 您需要在移動整合帳戶之後更新所有指令碼，以使用新的資源識別碼。

1. 選取 [瀏覽] ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. 在篩選搜尋方塊中輸入**整合**，然後從結果清單中選取 [整合帳戶] ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. 選取您想要刪除的**整合帳戶** ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4. 選取位於功能表上的 [移動] 連結 ![](./media/app-service-logic-enterprise-integration-accounts/move.png)
5. 確認您的選擇

## 後續步驟
- [深入了解合約](./app-service-logic-enterprise-integration-agreements.md "了解企業整合合約")


 

<!---HONumber=AcomDC_0713_2016-->