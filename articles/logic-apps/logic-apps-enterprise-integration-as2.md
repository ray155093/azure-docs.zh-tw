---
title: "了解如何建立企業整合套件的 AS2 合約 | Microsoft Docs"
description: "了解如何建立企業整合套件的 AS2 合約 | Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: 
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 47a1c5653d84a55224c18222bc547e1c863ea442
ms.openlocfilehash: ea256557a38b3ce6cb457d195fa4b48cfd8879b8


---
# <a name="enterprise-integration-with-as2"></a>具備 AS2 的企業整合
## <a name="create-an-as2-agreement"></a>建立 AS2 合約
若要在 Logic Apps 中使用企業功能，您必須先建立合約。 

### <a name="heres-what-you-need-before-you-get-started"></a>以下是您在開始之前的必要條件
* 在 Azure 訂用帳戶中定義一個[整合帳戶](../logic-apps/logic-apps-enterprise-integration-accounts.md)  
* 至少已經在整合帳戶中定義兩個[夥伴](logic-apps-enterprise-integration-partners.md)  

> [!NOTE]
> 建立合約時，合約檔案中的內容必須與合約類型相符。    
> 
> 

當您[建立整合帳戶](../logic-apps/logic-apps-enterprise-integration-accounts.md)並[新增夥伴](logic-apps-enterprise-integration-partners.md)之後，您可以依照下列步驟來建立合約：  

### <a name="from-the-azure-portal-home-page"></a>從 Azure 入口網站首頁
在您登入 [Azure 入口網站](http://portal.azure.com "Azure 入口網站")之後：  

1. 選取 [更多服務]，然後在篩選搜尋方塊中輸入**整合**。 從結果清單中選取 [整合帳戶]    
![](./media/logic-apps-enterprise-integration-agreements/overview-1.png)    
2. 選取要將憑證新增到的整合帳戶。 
![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
3. 選取 [合約]  圖格。 如果您沒有看到 [合約] 圖格，請先進行新增。   
![](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)   
4. 在開啟的 [合約] 刀鋒視窗中選取 [新增]  按鈕。  
![](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)  
5. 在 [合約] 刀鋒視窗中，輸入合約的**名稱**，然後從 [合約類型]、[主控夥伴]、[主控身分識別]、[來賓夥伴]、[來賓身分識別] 中選取 [AS2]。  
![](./media/logic-apps-enterprise-integration-agreements/agreement-3.png)  

以下是您在設定合約設定時可能覺得有用的一些詳細資訊： 

| 屬性 | 說明 |
| --- | --- |
| 主控夥伴 |合約需要主控夥伴和來賓夥伴。 主控夥伴代表設定合約的組織。 |
| 主控身分識別 |主控夥伴的識別碼。 |
| 來賓夥伴 |合約需要主控夥伴和來賓夥伴。 來賓夥伴代表與主控夥伴有生意往來的組織。 |
| 來賓身分識別 |來賓夥伴的識別碼。 |
| 接收設定 |這些屬性會套用到合約所接收的所有訊息 |
| 傳送設定 |這些屬性會套用到合約所傳送的所有訊息 |

讓我們繼續：  

1. 選取 [接收設定]  ，來設定處理透過此合約所接收訊息的方式。  
   
   * (選擇性) 您可以覆寫傳入訊息中的屬性。 若要這樣做，請選取 [覆寫訊息屬性]。
   * 如果您希望要求簽署所有傳入訊息，可選取 [訊息必須經過簽署]。 如果您選取此選項，就必須選取 [來賓夥伴公開憑證]，以驗證訊息上的簽章。
   * 如果您希望要求加密所有傳入訊息，可選取 [訊息必須加密]。  如果您選取此選項時，也必須需要選取 [主控夥伴私人憑證]，以解密傳入訊息。
   * 您也可以要求壓縮訊息。 若要這樣做，請選取 [訊息必須壓縮]。    
   * 選取 [傳送 MDN]，以針對所接收的訊息傳送同步 MDN
   * 選取 [傳送經過簽署的 MDN]，以針對所接收的訊息傳送經過簽署的 MDN
   * 選取 [傳送非同步 MDN]，以針對所接收的訊息傳送非同步 MDN     
    ![](./media/logic-apps-enterprise-integration-agreements/agreement-4.png)  

如果您想要深入了解接收設定啟用了哪些項目，請參閱下表。  

| 屬性 | 說明 |
| --- | --- |
| 覆寫訊息屬性 |選取此選項，以指出可覆寫所接收訊息中的屬性 |
| 訊息應該簽署 |啟用這個選項，以要求訊息必須經過數位簽署。  設定來賓夥伴公開憑證以進行簽章驗證  |
| 訊息應該加密 |啟用這個選項，以要求加密訊息。 未加密的訊息將會遭到拒絕。 設定主控夥伴私人憑證以解密訊息  |
| 訊息應該壓縮 |啟用這個選項，以要求壓縮訊息。 未壓縮的訊息將會遭到拒絕。 |
| MDN 文字 |這是預設要傳送給訊息傳送者的 MDN |
| 傳送 MDN |啟用這個選項，以允許傳送 MDN。 |
| 傳送簽署的 MDN |啟用這個選項，以要求簽署 MDN。 |
| MIC 演算法 | |
| 傳送非同步 MDN |啟用這個選項，以要求非同步傳送訊息。 |
| URL |這是要將 MDN 傳送到其中的 URL。 |

現在，讓我們繼續：  

1. 選取 [傳送設定]  ，以設定如何處理透過此合約傳送的訊息。  

   * 選取 [啟用訊息簽署]，以將經過簽署的訊息傳送給夥伴。 如果您選取此選項，就必須選取 [主控夥伴私人憑證 MIC 演算法] 和 [主控夥伴私人憑證]，以簽署訊息。
   * 選取 [啟用訊息加密]，以將加密的訊息傳送給夥伴。 如果您選取此選項，就必須選取 [來賓夥伴公開憑證演算法] 和 [來賓夥伴公開憑證]，以加密訊息。
   * 選取 [訊息必須壓縮] 以壓縮訊息 
   * 選取 [展開 HTTP 標頭]，以將 HTTP content-type 標頭展開為單一行 
   * 選取 [要求 MDN]，以針對所傳送的訊息接收同步 MDN
   * 選取 [要求經過簽署的 MDN]，以針對所傳送的訊息接收經過簽署的 MDN
   * 選取 [要求非同步 MDN]，以針對所傳送的訊息接收非同步 MDN。 如果您選取此選項，就必須提供要將 MDN 傳送到其中的 URL  
   * 選取 [啟用 NRR]，以啟用回條的不可否認性    
   ![](./media/logic-apps-enterprise-integration-agreements/agreement-5.png)  

如果您想要深入了解傳送設定啟用了哪些項目，請參閱下表。  

| 屬性 | 說明 |
| --- | --- |
| 啟用訊息簽署 |選取此選項，以簽署所有傳送自合約的訊息。 |
| MIC 演算法 |選取要用於訊息簽章的演算法。 設定主控夥伴私人憑證 MIC 演算法以簽署訊息 |
| 憑證 |選取要用於訊息簽章的憑證。 設定主控夥伴私人憑證以簽署訊息 |
| 啟用訊息加密 |選取此選項，以將所有傳送自此合約的訊息加密。 設定來賓夥伴公開憑證演算法以加密訊息 |
| 加密演算法 |選取要用於訊息加密的加密演算法。 設定來賓夥伴公開憑證以加密訊息 |
| 展開 HTTP 標頭 |選取此選項，以將 HTTP content-type 標頭展開為單一行 |
| 要求 MDN |啟用此選項，以針對所有傳送自此合約的訊息要求適用的 MDN |
| 要求簽署的 MDN |啟用以要求簽署所有傳送到此合約的 MDN |
| 要求非同步 MDN |啟用以要求將非同步 MDN 傳送到此合約 |
| URL |要將 MDN 傳送到其中的 URL |
| 啟用 NRR |選取此選項，以啟用回條的不可否認性 |

在 [整合帳戶] 刀鋒視窗上選取 [合約]  圖格，而您將會看到已列出新加入的合約。  
![](./media/logic-apps-enterprise-integration-agreements/agreement-6.png)

## <a name="next-steps"></a>後續步驟
* [深入了解企業整合套件](logic-apps-enterprise-integration-overview.md "了解企業整合套件")  


<!--HONumber=Jan17_HO4-->


