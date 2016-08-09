<properties 
	pageTitle="了解如何使用企業整合套件與 Logic Apps 編碼或解碼一般檔案 |Microsoft Azure App Service |Microsoft Azure" 
	description="使用企業整合套件與 Logic Apps 的功能編碼或解碼一般檔案" 
	services="app-service\logic" 
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

# 具備一般檔案的企業整合

## 概觀

您可以在會將 XML 內容編碼的邏輯應用程式內，使用一般檔案編碼連接器。您可能希望先將 XML 內容編碼，然後再傳送給 B2B 案例中的企業夥伴。您所建立的邏輯應用程式可從各種來源取得其 XML 內容，包括 HTTP 要求觸發程序、另一個應用程式，或甚至是這其中任一種[連接器](../connectors/apis-list.md)。如需 Logic Apps 功能的詳細資訊，請檢閱 [Logic Apps 文件](./app-service-logic-what-are-logic-apps.md "深入了解 Logic Apps")。

## 如何建立一般檔案編碼連接器

請遵循下列步驟來建立邏輯應用程式，並將一般檔案編碼連接器新增到邏輯應用程式

1. 建立邏輯應用程式並[將它連結到整合帳戶](./app-service-logic-enterprise-integration-accounts.md "了解如何將整合帳戶連結到邏輯應用程式")，其中包含用來將 XML 資料編碼的結構描述。
2. 將 [要求 - 收到 HTTP 要求時] 觸發程序新增到您的邏輯應用程式 ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)
3. 使用下列動作來新增將一般檔案編碼的動作：
-  選取**加**號
-  選取加號之後，選取所顯示的 [新增動作] 連結
-  在搜尋方塊中輸入「一般」，篩選所有動作以取得您想要使用的動作
-  從清單中選取 [將一般檔案編碼] 動作 ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)
6. 在快顯的 [將一般檔案編碼] 控制項上選取 [內容] 文字方塊 ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-3.png)
7. 選取內文標記做為您想要編碼的內容。內文標記將會填入內容欄位。![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-4.png)
8. 選取 [結構描述名稱] 清單方塊，然後選擇您想要用來將上述輸入內容編碼的結構描述 ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-5.png)
9. 儲存您的工作 ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)

此時，您已完成設定一般檔案編碼連接器。在真實世界應用程式中，您可以將編碼的資料儲存在 LOB 應用程式 (例如 SalesForce) 中，或者將編碼的資料傳送給交易夥伴。您可以使用所提供的任一個其他連接器，輕鬆地新增動作來將編碼動作的輸出傳送到 Salesforce，或傳送給交易夥伴。

您現在可以測試連接器，方法是向 HTTP 端點提出要求，並在要求內文中包含 XML 內容。

## 如何建立一般檔案解碼連接器

### 必要條件
您必須已將結構描述檔案上傳到您的整合帳戶，才能完成這些步驟。

1. 將 [要求 - 收到 HTTP 要求時] 觸發程序新增到您的邏輯應用程式 ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)
2. 使用下列動作來新增將一般檔案編碼的動作：
-  選取**加**號
-  選取加號之後，選取所顯示的 [新增動作] 連結
-  在搜尋方塊中輸入「一般」，篩選所有動作以取得您想要使用的動作
-  從清單中選取 [將一般檔案解碼] 動作 ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)
- 選取 [內容] 控制項。這將會從先前步驟中產生一份內容清單，讓您可用來做為要解碼的內容。您會注意到來自傳入 HTTP 要求的內文可用來做為要解碼的內容。請注意，您也可以將要解碼的內容直接輸入於 [內容] 控制項中。在此範例中，我選取了 [內文]，以使用傳入 HTTP 要求的內文 (來自解碼步驟的步驟 1)。
- 選取 [內文] 標記。請注意，內文標記目前位於內容控制項中。
- 選取您想要用來將內容解碼的結構描述名稱。在此範例中，我選擇了「OrderFile」。請注意，OrderFile 是我在先前某個時點上傳到整合帳戶的結構描述名稱。![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-decode-1.png)
- 從功能表中選取 [儲存] 來儲存您的工作 ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)

此時，您已完成設定一般檔案解碼連接器。在真實世界應用程式中，您可能想要在 LOB 應用程式 (例如 SalesForce) 中儲存已解碼的資料。您可以輕鬆新增動作，來將解碼動作的輸出傳送到 Salesforce。

您現在可以測試連接器，方法是向 HTTP 端點提出要求，並在要求內文中包含您想要解碼的 XML 內容。

## 詳細資訊
- [深入了解企業整合套件](./app-service-logic-enterprise-integration-overview.md "了解企業整合套件")

<!---HONumber=AcomDC_0727_2016-->