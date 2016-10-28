<properties 
	pageTitle="了解企業整合套件編碼 EDIFACT 訊息連接器 | Microsoft Azure App Service | Microsoft Azure" 
	description="了解如何使用夥伴搭配企業整合套件與 Logic Apps" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="padmavc" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/15/2016" 
	ms.author="padmavc"/>

# 開始使用編碼 EDIFACT 訊息

驗證 EDI 和夥伴特定屬性

## 建立連線

### 必要條件

* Azure 帳戶；您可以建立一個[免費帳戶](https://azure.microsoft.com/free)

* 需要有整合帳戶才能使用編碼 EDIFACT 訊息連接器。詳細資料請參閱如何建立[整合帳戶](./app-service-logic-enterprise-integration-create-integration-account.md)、[合作夥伴](./app-service-logic-enterprise-integration-partners.md)和 [EDIFACT 合約](./app-service-logic-enterprise-integration-edifact.md)

### 使用下列步驟連線至解碼 EDIFACT 訊息︰

1. [建立邏輯應用程式](./app-service-logic-create-a-logic-app.md)可提供範例。

2. 此連接器並沒有任何觸發程序。您可以使用其他觸發程序來啟動邏輯應用程式，例如 [要求] 觸發程序。在邏輯應用程式設計工具中，新增一個觸發程序，然後新增一個動作。從下拉式清單中選取 [顯示 Microsoft Managed API]，然後在搜尋方塊中輸入 "EDIFACT"。選取 [由合約名稱編碼 EDIFACT 訊息] 或是 [由身分識別編碼 EDIFACT 訊息]。

	![搜尋 EDIFACT](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage1.png)

3. 如果您之前尚未建立與整合帳戶的任何連線，系統將會提示您輸入連線詳細資料

	![建立整合帳戶連線](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage1.png)

4. 輸入整合帳戶詳細資料。具有星號的屬性為必要項目

	| 屬性 | 詳細資料 |
	| -------- | ------- |
	| 連線名稱 * | 為連線輸入任何名稱 |
	| 整合帳戶 * | 輸入整合帳戶名稱。請確定您的整合帳戶和邏輯應用程式位於相同的 Azure 位置 

	完成後，連線詳細資料看起來類似下圖

	![整合帳戶連線](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage2.png)
	
5. 選取 [建立]
	
6. 請注意，已建立連線

	![整合帳戶連線詳細資料](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage4.png)
	
#### 由合約名稱編碼 EDIFACT 訊息

7.	提供 EDIFACT 合約名稱和 xml 訊息以進行編碼。

	![提供必要欄位](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage6.png)
	
#### 由身分識別編碼 EDIFACT 訊息

7. 提供傳送者識別項、傳送者辨識符號、接收者識別項和接收者辨識符號，如 EDIFACT 合約中所設定。選取要編碼的 xml 訊息

	![提供必要欄位](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage7.png)
	
## EDIFACT 編碼處理以下項目

* 比對傳送者辨識符號和識別項，以及接收者辨識符號和識別項，以解析合約
* 序列化 EDI 交換，將 XML 編碼訊息轉換為交換中的 EDI 交易集。
* 套用交易集標頭和結尾區段
* 產生交換控制編號、群組控制編號和每個傳出交換的交易集控制編號
* 取代承載資料中的分隔符號
* 驗證 EDI 和夥伴特定屬性
	* 針對訊息結構描述進行交易集資料元素的結構描述驗證。
	* 對交易集資料元素執行 EDI 驗證。
	* 對交易集資料元素執行擴充驗證
* 產生每個交易集的 XML 文件。
* 要求技術和/或功能確認 (若已設定)。
	* 做為技術確認，CONTRL 訊息會指示交換的接收。
	* 做為功能確認，CONTRL 訊息會指示接受或拒絕接收的交換、群組或訊息，並列出錯誤或不支援的功能清單

## 後續步驟

[深入了解企業整合套件](./app-service-logic-enterprise-integration-overview.md "了解企業整合套件")

<!---HONumber=AcomDC_0824_2016-->