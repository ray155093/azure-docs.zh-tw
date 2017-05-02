---
title: "自 Azure 取得潛在客戶  | Microsoft Docs"
description: "本文說明 Microsoft 潛在客戶產生，以及如何連結您的 CRM 系統，進而整合您的準銷售案源與 Cloud Partner 入口網站。"
services: cloud-partner-portal
documentationcenter: 
author: Bigbrd
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: brdi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 6207695ede6c01f9423ddde7467d2a9b1ed35bd3
ms.lasthandoff: 04/18/2017


---

# <a name="get-customer-leads"></a>取得潛在客戶 
客戶是所有優秀企業的核心。 在現今的產品併購變革中，行銷人員需要著重在直接與客戶連結，建立關係。 這也是為什麼產生高品質潛在客戶，對您的銷售循環而言是一個相當關鍵的工具。
透過 Cloud Partner 入口網站，我們讓您在客戶對您的產品表達興趣，或部署您的產品後，能以程式化方式，立即收到客戶連絡資訊。 本文件中將說明所有您必須了解關於產生的潛在客戶的資訊、如何將潛在客戶連結到您的 CRM 系統，以及如何將他們整合至準銷售案源中。

## <a name="what-are-these-leads"></a>什麼是潛在客戶？
這些潛在客戶，是從 Marketplace 部署您的產品的客戶。 無論您的產品是列在 [Azure Marketplace ](https://azuremarketplace.microsoft.com/)或 [AppSource](https://appsource.com/)，您都可以收到對您的產品感興趣的潛在客戶。
### <a name="azure-marketplace"></a>Azure Marketplace
1.    客戶針對您的優惠進行「試用產品」。 試用產品是一個很好的機會，能讓您加快立即與潛在客戶分享商務產品的腳步，且試用過程毫無任何障礙。 所有試用產品，將產生潛在客戶，這些潛在客戶有興趣進一步了解您的產品。  在 [Azure Marketplace 試用產品](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf)深入了解試用產品。

    ![試用產品優惠](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

2.    客戶按一下 [立即取得] 按鈕後，即同意分享他們的資訊。 此類型潛在客戶是初始興趣潛在客戶，我們分享的這些客戶，都對取得您的產品感到有興趣，並且位居分享購買漏斗的最頂端。
 
    ![[立即取得] 按鈕](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

3.    客戶在 [Azure 入口網站](https://portal.azure.com/) 按下 [購買]，便正式取得您的產品。 此類型潛在客戶是主動潛在客戶，我們分享的這些客戶，都擁有 Azure 訂用帳戶，並開始部署您的實際產品。

    ![文字 [購買] 按鈕](./media/cloud-partner-portal-get-customer-leads/purchase-button.png) 

### <a name="appsource"></a>AppSource
1.    客戶針對您的優惠進行「試用產品」。 試用產品是一個很好的機會，能讓您加快立即與潛在客戶分享商務產品的腳步，且試用過程毫無任何障礙。 所有試用產品，將產生潛在客戶，這些潛在客戶有興趣進一步了解您的產品。  在 [AppSource 試用產品](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive)深入了解試用產品。

    ![試用產品優惠](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.    客戶按一下 [立即取得] 按鈕後，即同意分享他們的資訊。 此類型潛在客戶是初始興趣潛在客戶，我們分享的這些客戶，都對取得您的產品感到有興趣，並且位居分享購買漏斗的最頂端。

    ![[立即取得] 按鈕](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)

3.    客戶按下您優惠上的 [與我連絡] 按鈕。 此類型潛在客戶是主動潛在客戶，我們分享的這些客戶主動要求持續追蹤您的產品。

    ![與我連絡](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

## <a name="lead-data"></a>潛在客戶資料
每一位您收到的潛在客戶，都擁有特定欄位，欄位中傳遞若干資料。 由於您所獲得的潛在客戶，都位在其購買過程中特定不同階段，處理這些潛在客戶的最佳方法是刪除重複資料，並個人化後續動作。 透過這個方式，每位客戶都能獲得適當的訊息，而您每次都能打造獨特的客戶關係。

|欄位名稱|範例|
|----------|--------|
|LeadSource| 格式為 來源-動作&#124;優惠 <br/><br/>**來源**：<br/>「AzureMarketplace 」、<br/>「Azure 入口網站」、<br/>「試用產品」、 <br/>「SPZA」(AppSource 的縮寫)<br/><br/>**動作**：<br/>「INS」 – 代表安裝。 這代表客戶在 Azure Marketplace 或 AppSource按下按鈕，取得您的產品。<br/><br/>「PLT」 –  代表合作夥伴主導的試用版。 這代表客戶在 AppSource 按下「與我連絡」按鈕。<br/><br/>「DNC」 – 代表請勿連絡。 這代表在 AppSource，交叉列於您的應用程式頁面的合作夥伴，收到連絡要求。 我們分享此客戶交叉列於您的應用程式的通知，但無須連絡他們。<br/><br/>「建立」 – 這僅位在 Azure 入口網站內，當客戶購買您的優惠至帳戶中時會出現。<br/><br/>「開始試用產品」 – 這僅限試用產品，代表一名客戶開始進行產品試用。<br/><br/>優惠<br/>    「checkpoint.check-point-r77-10sg-byol」、<br/>「bitnami.openedxcypress」、<br/>「docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a」<br/> <br/> 這些是您和您的特定優惠的指定唯一識別碼。 |
|客戶資訊|    {<br/>「名」：「John」、<br/>「姓」：「Smith」、<br/>「電子郵件」：「jsmith@microsoft.com」、<br/>「電話」：「1234567890」、<br/>「國家」：「美國」、<br/>「公司」：「Microsoft」、<br/>「職稱」：「技術長」<br/>}<br/><br/>附註︰並非每名潛在客戶都擁有所有資料|


我們目前正積極改善潛在客戶功能，因此，如果您在這裡沒有看到一些您想要的資料欄位，請[傳送您的意見反映給我們](mailto:AzureMarketOnboard@microsoft.com)。

## <a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>如何連接 CRM 系統與 Cloud Partner 入口網站 
若要開始獲得潛在客戶，我們在 Cloud Partner 入口網站上建立潛在客戶管理連接器，如此您可以輕易導入您的 CRM 資訊，讓我們為您進行連接。 現在您無須耗費浩大的工程心力來整合外部系統，便可以輕鬆地運用由 Marketplace 產生的潛在客戶。
 
![潛在客戶管理連接器](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png) 我們可以將潛在客戶寫入各種 CRM 系統，或直接寫入 Azure 儲存體資料表，您可以在資料表中隨心所欲管理潛在客戶。 以下按步驟說明如何連結每一個潛在客戶目的地：<使用更新版 markdown 檔案，做為來自 appsource/試用產品的連結！ 重新查閱潛在客戶連接 現在在個別頁面需要非 JSON 欄位>

*    Dynamics CRM Online-[按一下這裡](./cloud-partner-portal-lead-management-instructions-dynamics.md)以取得如何設定 Dynamics CRM Online 來獲得潛在客戶的說明。 
*    Marketo-[按一下這裡](./cloud-partner-portal-lead-management-instructions-marketo.md)以取得如何設定 Marketo Lead 組態來獲得潛在客戶的說明。
*    Salesforce-[按一下這裡](./cloud-partner-portal-lead-management-instructions-salesforce.md)以取得如何設定 Salesforce 執行個體來獲得潛在客戶的說明。
*    Azure 資料表-[按一下這裡](./cloud-partner-portal-lead-management-instructions-azure-table.md)以取得如何設定 Azure 儲存體帳戶，並在 Azure 資料表獲得潛在客戶的說明。 

您妥善設定您的潛在客戶目的地，並按下您優惠上的 [發佈] 後，我們將驗證連接，並傳送測試用潛在客戶給您。 在上架前檢視優惠時，您也可以試著在預覽環境中自行取得優惠，藉此測試您的潛在客戶連接。  關鍵是，請務必確定您的潛在客戶設定保持在最新狀態，如此您將不會流失潛在客戶；因此您的商務若有變更，請記得更新這些連接。

## <a name="what-next"></a>接下來呢？ 
技術設定完成後，您應該將這些潛在客戶整合至您目前的銷售與行銷策略，以及營運流程當中。 我們非常感興趣進一步了解您的整體銷售流程，並想與您密切合作，為您提供高品質潛在客戶及足夠的資料，讓您得以成功。 我們歡迎您針對我們如何能最佳化及改善我們傳送給您的潛在客戶，及提供額外資料協助這些客戶達成您的成就，提出您的意見反應。
如果您有興趣[提供意見反應](mailto:AzureMarketOnboard@microsoft.com)及建議，讓我們的銷售團隊能推出更成功的 Marketplace 潛在客戶，請告訴我們。




