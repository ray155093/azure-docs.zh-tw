---
title: "在 Azure 邏輯應用程式中建立 AS2 合約 | Microsoft Docs"
description: "為企業整合套件建立 AS2 合約 | Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 6a947ea997bbcfe1b6b28c7cbb49911836750e6a
ms.openlocfilehash: a490b89c5420aecdfb3f79289979faab9a4630e9


---
# <a name="enterprise-integration-with-as2"></a>具備 AS2 的企業整合
若要在邏輯應用程式中使用企業功能，您必須先建立合約。

## <a name="prerequisites"></a>必要條件
* 必須在您的 Azure 訂用帳戶中定義一個[整合帳戶](../logic-apps/logic-apps-enterprise-integration-accounts.md)。  
* 至少必須已經在您的整合帳戶中定義兩個[夥伴](logic-apps-enterprise-integration-partners.md)。  

> [!NOTE]
> 當您建立合約時，合約檔案中的內容必須與合約類型相符。    

當您建立整合帳戶並新增夥伴之後，您可以使用以下各節中的程序來建立合約。  

## <a name="create-an-agreement"></a>建立合約

1. 登入 [Azure 入口網站](http://portal.azure.com "Azure 入口網站")。  
2. 選取 [更多服務]，在篩選搜尋方塊中輸入**整合**，然後在結果清單中選取 [整合帳戶]。

 ![在結果清單中選取 [整合帳戶]](./media/logic-apps-enterprise-integration-agreements/overview-1.png)    
3. 選取要將憑證新增到其中的整合帳戶。

 ![選取整合帳戶](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. 選取 [合約] 圖格。 若圖格未顯示，請新增它。

 ![選取 [合約] 圖格](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)   
5. 在 [合約] 刀鋒視窗上，選取 [新增]。

 ![選取 [新增]](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)  
6. 輸入您的合約名稱，在 [合約類型] 清單中選取 [AS2]，並在 [主機合作夥伴]、[主機別識]、[來賓合作夥伴] 與 [來賓別識] 清單中輸入適當資訊。

 ![輸入合約的名稱](./media/logic-apps-enterprise-integration-agreements/agreement-3.png)  

 下表說明 [新增] 對話方塊中的屬性：

    | 屬性 | 說明 |
    | --- | --- |
    | 主控夥伴 | 合約需要主機合作夥伴和來賓合作夥伴。 主機合作夥伴代表設定合約的組織。 |
    | 主控身分識別 | 主控夥伴的識別碼。 |
    | 來賓夥伴 | 合約需要主機合作夥伴和來賓合作夥伴。 來賓夥伴代表與主控夥伴有生意往來的組織。 |
    | 來賓身分識別 | 來賓夥伴的識別碼。 |
    | 接收設定 | 適用於合約接收之所有訊息的屬性。 |
    | 傳送設定 | 適用於合約傳送之所有訊息的屬性。 |

7. 若要設定透過此合約接收之訊息的處理方式，請執行下列動作：

 a. 選取 [接收設定]。

 b. 您也可以透過選取 [覆寫訊息屬性] 核取方塊來覆寫內送訊息的屬性。

 c. 若要要求所有內送訊息都必須經過簽署，請選取 [訊息必須經過簽署] 核取方塊。 如果您選取此選項，請透過選取 [憑證] 清單中的 [來賓合作夥伴公開憑證] 以驗證訊息上的簽章。

 d. 若要要求加密所有內送訊息，請選取 [訊息必須加密] 核取方塊。 如果您選取此選項，請透過選取 [憑證] 清單中的 [主機合作夥伴私人憑證] 以將內送訊息解密。

 e. 若要要求訊息必須壓縮，請選取 [訊息必須壓縮] 核取方塊。    

 f. 若要針對已接收的訊息傳送同步郵件處置通知 (MDN)，請選取 [傳送 MDN] 核取方塊。

 g. 若要針對已接收的訊息傳送已簽署的 MDN，請選取 [傳送經過簽署的 MDN] 核取方塊。

 h. 若要針對已接收的訊息傳送非同步 MDN，請選取 [傳送非同步 MDN] 核取方塊。

 ![設定 [接收設定] 屬性](./media/logic-apps-enterprise-integration-agreements/agreement-4.png)  

 下表說明**接收設定**屬性：  

 | 屬性 | 說明 |
 | --- | --- |
 | 覆寫訊息屬性 | 指出所接收訊息中可被覆寫的屬性。 |
 | 訊息應該簽署 | 要求訊息必須經過數位簽署。 設定來賓合作夥伴公開憑證以進行簽章驗證。  |
 | 訊息應該加密 | 要求訊息必須經過加密。 未加密的訊息將會遭到拒絕。 設定主機合作夥伴私人憑證以進行訊息解密。  |
 | 訊息應該壓縮 | 要求訊息必須經過壓縮。 未壓縮的訊息將會遭到拒絕。 |
 | MDN 文字 | 要傳送給郵件寄件者的預設郵件處置通知 (MDN)。 |
 | 傳送 MDN | 要求傳送 MDN。 |
 | 傳送簽署的 MDN | 要求簽署 MDN。 |
 | MIC 演算法 | |
 | 傳送非同步 MDN | 要求以非同步方式傳送訊息。 |
 | URL | MDN 的傳送目標 URL。 |

8. 若要設定透過此合約傳送之訊息的處理方式，請執行下列動作：

 a. 選取 [傳送設定]。  

 b. 若傳送已簽署的訊息給合作夥伴，請選取 [啟用訊息簽署] 核取方塊。 若選取此選項，請透過選取 [MIC 演算法] 清單中的 [主機合作夥伴私人憑證 MIC 演算法] 與 [憑證] 清單中的 [主機合作夥伴私人憑證] 以簽署訊息。

 c. 若要傳送加密的訊息給該合作夥伴，請選取 [啟用訊息加密] 核取方塊。 若選取此選項，請透過選取 [加密演算法] 清單中的 [來賓合作夥伴公開憑證演算法] 與 [憑證] 清單中的 [來賓合作夥伴公開憑證] 以加密訊息。

 d. 若要壓縮訊息，請選取 [啟用訊息壓縮] 核取方塊。

 e. 若要將 HTTP 內容類型標頭展開為單一檔案，請選取 [展開 HTTP 標頭] 核取方塊。

 f. 若要接收所傳送訊息的同步 MDN，請選取 [要求 MDN] 核取方塊。

 g. 若要接收所傳送訊息的已簽署 MDN，請選取 [要求經過簽署的 MDN] 核取方塊。

 h. 若要接收所傳送訊息的非同步 MDN，請選取 [要求非同步 MDN] 核取方塊。 若選取此選項，請輸入 MDN 傳送目標 URL。  

 i. 若要要求接收的不可否認性，請選取 [啟用 NRR] 核取方塊。

 j. 選取 [確定] 。

 ![設定 [傳送設定] 屬性](./media/logic-apps-enterprise-integration-agreements/agreement-5.png)  

 下表說明**傳送設定**屬性：  

 | 屬性 | 說明 |
 | --- | --- |
 | 啟用訊息簽署 | 要求傳送自合約的所有訊息都必須經過簽署。 |
 | MIC 演算法 | 要用來簽署訊息的演算法。 設定主機合作夥伴私人憑證 MIC 演算法以簽署訊息。 |
 | 憑證 | 要用於簽署訊息的憑證。 設定主機合作夥伴私人憑證以簽署訊息。 |
 | 啟用訊息加密 | 要求傳送自此合約的所有訊息都必須經過加密。 設定來賓合作夥伴公開憑證演算法以加密訊息。 |
 | 加密演算法 | 要用於訊息加密的加密演算法。 設定來賓合作夥伴公開憑證以加密訊息。 |
 | 憑證 | 要用於加密訊息的憑證。 設定來賓合作夥伴私人憑證以加密訊息。 |
 | 啟用訊息壓縮 | 要求傳送自此合約的所有訊息都必須經過壓縮。 |
 | 展開 HTTP 標頭 | 請將 HTTP 內容類型標頭放在一行中。 |
 | 要求 MDN | 針對傳送自此合約的所有訊息要求 MDN。 |
 | 要求簽署的 MDN | 要求傳送到此合約的所有 MDN 都必須經過簽署。 |
 | 要求非同步 MDN | 要求將非同步 MDN 傳送到此合約。 |
 | URL | MDN 的傳送目標 URL。 |
 | 啟用 NRR | 要求接收的不可否認性 (NRR)，這是一種通訊屬性，它會提供資料已由預訂收件者接收的證據。 |

## <a name="view-the-agreements-list"></a>檢視合約清單
若要檢視新增的合約，請選取 [整合帳戶] 刀鋒視窗上的 [合約] 圖格。

![檢視 [合約] 清單](./media/logic-apps-enterprise-integration-agreements/agreement-6.png)

## <a name="next-steps"></a>後續步驟
* [深入了解企業整合套件](logic-apps-enterprise-integration-overview.md "了解企業整合套件")  



<!--HONumber=Feb17_HO1-->


