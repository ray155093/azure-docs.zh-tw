<properties
   pageTitle="常見問題集"
   description="Power BI Embedded 常見問題集"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="06/28/2016"
   ms.author="owend"/>

# Power BI Embedded 常見問題集

1. **您在 Build 中發佈了哪些與 Power BI 有關的事項？**

    Microsoft 宣布已開始提供 Microsoft Power BI Embedded 服務公開預覽。

2.	**何謂 Microsoft Power BI Embedded？**

    Microsoft Power BI Embedded 是一項 Azure 服務，可讓應用程式開發人員在面對客戶的應用程式中，內嵌令人讚嘆、具全面互動性的報表和視覺效果，不需要花費時間與金錢從頭開始自行建置控制項。您可以從立即可用的大量資料視覺效果中選擇，或輕鬆建置自訂的視覺效果，來滿足您應用程式的獨特需求。Power BI Embedded 是針對讓想要在較大型應用程式中向客戶提供 BI 的 ISV/應用程式開發人員使用所設計。

3.	**誰會想要以及為何想要使用 Microsoft Power BI Embedded？**

    Microsoft Power BI Embedded 是針對想要跨任何裝置為使用者提供令人讚嘆且互動式的資料視覺效果體驗，但不想自行建置視覺效果體驗的應用程式開發人員所設計。使用 Power BI Embedded，開發人員就可以使用「直接查詢」持續提供最新的檢視。開發人員也可以使用 Azure ARM API 和 Power BI API，以程式設計方式部署、管理及自動化 Power BI。和 Power BI 的所有項目一樣，內嵌服務會自動調整以符合您應用程式的使用狀況和需求。Power BI Embedded 服務具有依據使用量計費基礎的定價模型特色。對於定價，請參閱[此服務如何定價？](#price)。

4.	**Power BI Embedded 與 Power BI 服務之間的關聯為何？**

    Power BI Embedded 和 Power BI 服務是不同的供應項目。Power BI Embedded 具有以使用量為基礎的計費模型和透過 Azure 入口網站部署等特色，並且是針對讓 ISV 能夠在應用程式中內嵌資料視覺效果以供其客戶使用所設計。Power BI 服務是透過 O365 入口網站計費和部署，為獨立的一般目的 BI 供應項目，主要目標鎖定在企業內部用途。如需 Power BI 服務的相關詳細資訊，請造訪 [www.powerbi.com](www.powerbi.com)。

5.	**為什麼 Power BI Embedded 可改善我的應用程式？**

    當您在應用程式中利用資料視覺效果直接告知使用者決策時，應用程式明顯變得更加強大。Power BI Embedded 可讓您使用互動式、永遠最新及豐富的資料視覺效果來增強您的應用程式，讓您可以在任何裝置上輕鬆提高應用程式使用率、使用者滿意度和忠誠度，以及提供內容相關分析。

6.	**有沒有任何關於如何在應用程式中使用 Power BI Embedded 的規則或限制？**

    您可以在自己開發的應用程式中使用 Power BI Embedded 服務，唯該應用程式 (1) 將主要或重要功能加入 Power BI Embedded 的服務中，且其主要目的非取代任何 Power BI 服務，及 (2) 僅供外部使用者使用。您不得在內部商務應用程式內使用 Power BI Embedded 服務。

7.	**能否使用 Power BI Embedded 建立內部應用程式？**

    不行，Power BI Embedded 僅供外部使用者使用，不能在內部商務應用程式內使用。若要將 Power BI 內容內嵌為供內部商務應用程式使用，則您應該使用 Power BI 服務，而使用該內容的所有使用者皆必須具備有效的 Power BI Free 或 Power BI Pro 使用者訂閱授權。如需使用 Power BI 服務進行內部內嵌的詳細資訊，請前往 [https://dev.powerbi.com](https://dev.powerbi.com)。

8.	**這是全球都可使用的服務嗎？**

    北美地區或我們在 BUILD 2016 (於 US South Central Data Center 舉行) 中宣布的地區可以使用 Power BI Embedded 服務。您可以預期我們很快就會在其他 Azure 資料中心推出這項服務。

9. **此服務可用的 SLA 是？**

    Power BI Embedded 現在是以預覽的 Azure 服務方式提供使用，並沒有正式的 SLA。當服務從預覽版本變成正式推出版本時就會提供 SLA。

10. <a name="price"/>**此服務是以何種方式定價？**

    Power BI Embedded 目前為預覽版本，2016 年 5 月 1 日前免費提供使用。從 2016 年 5 月 1 日開始，此服務會按每次轉譯計費。客戶可以透過兩個主要授權方式購買此服務：Microsoft 線上訂閱方案 (MOSP) 或企業大量授權方案。

    您可以在自己開發的應用程式中使用 Power BI Embedded 服務，唯該應用程式 (1) 將主要或重要功能加入我們的服務中，且其主要目的非取代任何 Power BI 服務，及 (2) 僅供外部使用者使用。您不得在內部商務應用程式內使用 Power BI Embedded 服務。


   ![](media\power-bi-embedded-faq\price.png)

11. **什麼是轉譯？如何計費？**

    向要求查詢服務的使用者顯示視覺校果時，就會發生轉譯。Power BI 服務會盡可能嘗試快取轉譯的內容，以減少您的應用程式將被據以收費的轉譯數目。但是，使用者動作 (例如篩選) 將會導致向我們的服務送出查詢，這可能會構成新的轉譯。

    例如，如果使用者檢視包含四個視覺效果的報表，這可能會導致產師四個轉譯。如果使用者重新整理報表，並向服務傳送更多查詢，就會導致再度產生四個轉譯。服務擁有者能夠掌控終端使用者驅動新查詢 (其會導致付費轉譯) 的程度，以在靜態資料案例中限制成本暴露程度及降低成本。

    轉譯按每 1,000 次轉譯計費。轉譯數量低於 1,000 則按比例攤派費用。客戶每月可獲得 1,000 次免費轉譯。透過大量授權合約購買服務的客戶，應向其 Microsoft 合作夥伴或銷售商洽詢定價資訊。

12. **是否有提供任何工具或指導方針以協助我預估應該預期會有多少轉譯？ 如何知道已經完成多少轉譯？**

    Azure 入口網站將針對您的訂用帳戶提供已經執行多少轉譯的相關計費詳細資料。

13. **是否需要有 Power BI 訂用帳戶，才能使用 Power BI Embedded 來開發應用程式？ 如何開始使用？**

    身為應用程式開發人員，您不需要有 Power BI 訂用帳戶就能夠建立您想要在應用程式中使用的報表與視覺效果。您將需要 Microsoft Azure 訂用帳戶和免費的 Power BI Desktop 應用程式

    請參閱我們的服務文件了解如何使用 Power BI Embedded 服務的相關資訊。

14. **我有 Azure 訂用帳戶。我可以利用現有的訂用帳戶來使用 Power BI Embedded 嗎？**

    是。您可以使用現有的 Azure 訂用帳戶來佈建及使用 Microsoft Power BI Embedded 服務。

15. **我的應用程式的使用者是否需要 Power BI 授權？**

    不用。您的應用程式的使用者不需要購買個別的 Power BI 訂用帳戶就能夠存取應用程式內資料視覺效果。在 Power BI Embedded 模型中，應用程式提供者將透過 Azure 使用量計量表來為服務計費。請參閱[定價和授權頁面](http://go.microsoft.com/fwlink/?LinkId=760527)。

16. **如何使用 Power BI Embedded 執行使用者驗證工作？**

    Power BI Embedded 服務是使用應用程式權杖進行驗證和授權，而不是使用明確的使用者驗證。在應用程式權杖模型中，您的應用程式會管理您的使用者的驗證與授權。然後，您的應用程式可以在必要時建立及傳送應用程式權杖，以告知我們的服務轉譯要求的報表。雖然您可以使用 Azure Active Directory 來進行使用者驗證與授權，但這項設計不需要您的應用程式這樣做。如需應用程式權杖的詳細資訊，請參閱[應用程式權杖](https://azure.microsoft.com/zh-TW/documentation/articles/power-bi-embedded-get-started-sample/#key-flow)文件頁面。

17. **Power BI Embedded 目前支援哪些資料來源？**

    在服務公開預覽期間，我們將支援透過「直接查詢」使用基本認證來存取雲端資料來源。這表示目前支援像是 Azure SQL DB、HDInsight Spark 及 Azure SQL DW 這樣的來源。我們將在未來幾個月內新增對其他資料來源和存取類型的支援。我們將在 Power BI 開發人員網站 ([http://dev.powerbi.com](http://dev.powerbi.com/)) 宣布新支援的資料來源。

18. **租用模型如何與 Power BI Embedded 搭配使用？**

    在 Power BI Embedded 模型中，沒有明確要求您的客戶必須是 Azure Active Directory (Azure AD) 租用戶。您可以選擇是否要為您的客戶要求 Azure AD。因此，您應用程式和基礎結構的架構將決定 Power BI Embedded 所需的租用模型。

    建置您應用程式的開發人員/員工將需要有 Azure AD 使用者帳戶，他們才能透過 Azure 入口網站管理您的 Azure 訂用帳戶和工作區集合。可讓開發人員匯入報表、修改連接字串，以及讓內嵌 URL 改為使用應用程式權杖進行驗證，因此不需要 Azure AD 的程式設計 API。您可以在 [Azure.com 的 Power BI Embedded 文件頁面](https://azure.microsoft.com/zh-TW/documentation/services/power-bi-embedded/)，找到如何使用我們 API 和 Azure 入口網站的詳細資料。

19. **哪裡可以深入了解？**

    您可以造訪 [Power BI Embedded 文件頁面](http://go.microsoft.com/fwlink/?LinkId=760526)。您可以透過造訪 [Power BI 開發人員部落格](http://blogs.msdn.com/powerbidev)或造訪 Power BI 開發人員中心 (dev.powerbi.com)，來取得此服務的相關最新資訊。您也可以在 [Stackoverflow](http://stackoverflow.com/questions/tagged/powerbi) 提出問題。

20. **如何開始使用？**

    您現在就可以免費開始使用！ 如果您有 Azure 訂用帳戶，您現在就可以直接從 Azure 入口網站佈建 Power BI Embedded。您也可以建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。在您佈建 Power BI Embedded 服務之後，就能夠輕鬆地直接使用 Power BI REST API，或使用 [GitHub](http://go.microsoft.com/fwlink/?LinkID=746472) 上提供的開發人員 SDK。也有提供範例說明如何使用開發人員 SDK。

## 另請參閱

- [何謂 Microsoft Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)
- [開始使用 Microsoft Power BI Embedded 預覽](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0629_2016-->