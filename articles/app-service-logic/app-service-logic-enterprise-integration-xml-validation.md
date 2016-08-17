<properties 
	pageTitle="企業整合套件中 XML 驗證的概觀 | Microsoft Azure App Service | Microsoft Azure" 
	description="了解驗證如何在企業整合套件與 Logic Apps 中運作" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# 具備 XML 驗證的企業整合

## 概觀
在 B2B 案例中，合約的夥伴通常需要先驗證彼此間所交換訊息是有效的，才能開始處理資料。在企業整合套件中，您可以使用 XML 驗證連接器，根據預先定義的結構描述來驗證文件。

## 如何利用 XML 驗證連接器驗證文件
1. 建立邏輯應用程式並[將它連結到整合帳戶](./app-service-logic-enterprise-integration-accounts.md "了解如何將整合帳戶連結到邏輯應用程式")，其中包含用來驗證 XML 資料的結構描述。
2. 將 [要求 - 收到 HTTP 要求時] 觸發程序新增到您的邏輯應用程式 ![](./media/app-service-logic-enterprise-integration-xml/xml-1.png)
3. 先選取 [新增動作] 來新增 [XML 驗證] 動作
4. 在搜尋方塊中輸入「xml」，篩選所有動作以取得您想要使用的動作
5. 選取 [XML 驗證] ![](./media/app-service-logic-enterprise-integration-xml/xml-2.png)
6. 選取 [內容] 文字方塊 ![](./media/app-service-logic-enterprise-integration-xml/xml-1-5.png)
7. 選取內文標記做為要驗證的內容。![](./media/app-service-logic-enterprise-integration-xml/xml-3.png)
8. 選取 [結構描述名稱] 清單方塊，然後選擇您想要用來驗證上述輸入內容的結構描述 ![](./media/app-service-logic-enterprise-integration-xml/xml-4.png)
9. 儲存您的工作 ![](./media/app-service-logic-enterprise-integration-xml/xml-5.png)

此時，您已完成設定驗證連接器。在真實世界應用程式中，您可能想要在 LOB 應用程式 (例如 SalesForce) 中儲存已驗證的資料。您可以輕鬆新增動作，來將驗證的輸出傳送到 Salesforce。

您現在可以藉由向 HTTP 端點提出要求來測試驗證動作。

## 後續步驟

[深入了解企業整合套件](./app-service-logic-enterprise-integration-overview.md "了解企業整合套件")

<!---HONumber=AcomDC_0803_2016-->