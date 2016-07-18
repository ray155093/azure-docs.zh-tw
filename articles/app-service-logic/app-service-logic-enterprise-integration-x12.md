<properties 
	pageTitle="X12 與企業整合套件的概觀 | Microsoft Azure App Service" 
	description="了解如何使用 X12 合約來建立 Logic Apps" 
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
	ms.date="06/29/2016" 
	ms.author="deonhe"/>

# 具備 X12 的企業整合 

## 建立 X12 合約 
在您可以交換 X12 訊息之前，您必須先建立 X12 合約，並將它儲存在您的整合帳戶中。下列步驟將逐步引導您完成建立 X12 合約的程序。

### 以下是您在開始之前的必要條件
- 在 Azure 訂用帳戶中定義一個[整合帳戶](./app-service-logic-enterprise-integration-accounts.md)
- 至少已經在整合帳戶中定義兩個[夥伴](./app-service-logic-enterprise-integration-partners.md)

>[AZURE.NOTE]建立合約時，合約檔案中的內容必須與合約類型相符。


當您[建立整合帳戶](./app-service-logic-enterprise-integration-accounts.md)並[新增夥伴](./app-service-logic-enterprise-integration-partners.md)之後，您可以依照下列步驟來建立 X12 合約：

### 從 Azure 入口網站首頁

在您登入 [Azure 入口網站](http://portal.azure.com "Azure 入口網站")之後：
1. 從左邊功能表選取 [瀏覽]。

>[AZURE.TIP]如果您沒有看到 [瀏覽] 連結，可能需要先展開功能表。選取摺疊功能表左上方的 [顯示功能表] 連結，來執行此動作。

![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. 在篩選搜尋方塊中輸入*整合*，然後從結果清單中選取 [整合帳戶]。![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. 在開啟的 [整合帳戶] 刀鋒視窗中，選取您要在其中建立合約的整合帳戶。如果您沒有看到任何整合帳戶清單，請[先建立一個](./app-service-logic-enterprise-integration-accounts.md "所有與整合帳戶相關的資訊")。![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  選取 [合約] 圖格。如果您沒有看到 [合約] 圖格，請先進行新增。![](./media/app-service-logic-enterprise-integration-agreements/agreement-1.png)
5. 在開啟的 [合約] 刀鋒視窗中選取 [新增] 按鈕。![](./media/app-service-logic-enterprise-integration-agreements/agreement-2.png)
6. 在開啟的 [合約] 刀鋒視窗中，輸入合約的**名稱**，然後選取 [合約類型]、[主控夥伴]、[主控身分識別]、[來賓夥伴]、[來賓身分識別]。![](./media/app-service-logic-enterprise-integration-x12/x12-1.png)

以下是您在設定合約設定時可能覺得有用的一些詳細資訊：
  
|屬性|說明|
|----|----|
|主控夥伴|合約需要主控夥伴和來賓夥伴。主控夥伴代表設定合約的組織。|
|主控身分識別|主控夥伴的識別碼。 |
|來賓夥伴|合約需要主控夥伴和來賓夥伴。來賓夥伴代表與主控夥伴有生意往來的組織。|
|來賓身分識別|來賓夥伴的識別碼。|
|接收設定|這些屬性會套用到合約所接收的所有訊息|
|傳送設定|這些屬性會套用到合約所傳送的所有訊息|  
讓我們繼續：
7. 選取 [接收設定]，來設定處理透過此合約所接收訊息的方式。
 
 - (選擇性) 您可以覆寫傳入訊息中的屬性。若要這樣做，請選取 [覆寫訊息屬性] 核取方塊。
  - 如果您希望要求簽署所有傳入訊息，選取 [訊息應該簽署] 核取方塊。如果您選取此選項，也必須選取將用來驗證訊息簽章的**憑證**。
  - (選擇性) 您也可以要求加密訊息。若要這樣做，請選取 [訊息應該加密] 核取方塊。然後您需要選取將用來為傳入訊息解碼的**憑證**。
  - 您也可以要求壓縮訊息。若要這樣做，請選取 [訊息應該壓縮] 核取方塊。![](./media/app-service-logic-enterprise-integration-agreements/agreement-4.png)

如果您想要深入了解接收設定啟用了哪些項目，請參閱下表。

|屬性|說明|
|----|----|
|覆寫訊息屬性|選取此選項，以指出可覆寫所接收訊息中的屬性 |
|訊息應該簽署|啟用這個選項，以要求訊息必須經過數位簽署|
|訊息應該加密|啟用這個選項，以要求加密訊息。未加密的訊息將會遭到拒絕。|
|訊息應該壓縮|啟用這個選項，以要求壓縮訊息。未壓縮的訊息將會遭到拒絕。|
|MDN 文字|這是預設要傳送給訊息傳送者的 MDN|
|傳送 MDN|啟用這個選項，以允許傳送 MDN。|
|傳送簽署的 MDN|啟用這個選項，以要求簽署 MDN。|
|MIC 演算法||
|傳送非同步 MDN|啟用這個選項，以要求非同步傳送訊息。|
|URL|這是要將訊息傳送到其中的 URL。|
現在，讓我們繼續：
8. 選取 [傳送設定]，來設定處理透過此合約所傳送訊息的方式。![](./media/app-service-logic-enterprise-integration-agreements/agreement-5.png)

如果您想要深入了解傳送設定啟用了哪些項目，請參閱下表。

|屬性|說明|
|----|----|
|啟用訊息簽署|選取此核取方塊，以簽署所有傳送自合約的訊息。|
|MIC 演算法|選取要在訊息簽章中使用的演算法|
|憑證|選取要在訊息簽章中使用的憑證|
|啟用訊息加密|選取此核取方塊，以將所有傳送自此合約的訊息加密。|
|加密演算法|選取要在訊息加密中使用的加密演算法|
|展開 HTTP 標頭|選取此核取方塊，將 HTTP content-type 標頭展開為單一行。|
|要求 MDN|啟用此核取方塊，以針對所有傳送自此合約的訊息要求適用的 MDN|
|要求簽署的 MDN|啟用以要求簽署所有傳送到此合約的 MDN|
|要求非同步 MDN|啟用以要求將非同步 MDN 傳送到此合約|
|URL|要將 MDN 傳送到其中的 URL|
|啟用 NRR|選取此核取方塊，以啟用回條的不可否認性|
就快要完成了！
9. 在 [整合帳戶] 刀鋒視窗上選取 [合約] 圖格，而您將會看到已列出新加入的合約。![](./media/app-service-logic-enterprise-integration-agreements/agreement-6.png)



## 詳細資訊
- [深入了解企業整合套件](./app-service-logic-enterprise-integration-overview.md "了解企業整合套件")

<!---HONumber=AcomDC_0706_2016-->